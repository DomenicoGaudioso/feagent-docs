---
layout: default
title: "59 - Surrogate models and reduced models"
parent: English
nav_order: 59
---

# 59 - Surrogate models and reduced models

feagent builds two kinds of "light" model from a full model, stating **which
analyses and which results they must reproduce faithfully**:

| | Surrogate model (`feagent.surrogate`) | Reduced model (`feagent.reduction`) |
|---|---|---|
| What it reproduces | some scalar results as some parameters vary | stiffness and mass of the model on a few DOFs |
| How | samples of feagent analyses and regression | matrix algebra (Guyan, Craig-Bampton) |
| Typical use | parametric studies, optimisation, uncertainty, sensitivity | substructures (piers, foundations, decks), fast dynamics |
| Fidelity | tolerance per target, checked by cross-validation | exact in statics (Guyan); frequencies within tolerance (Craig-Bampton) |

## Surrogate model

### Parameters

A parameter is a model quantity that varies within a range
(`Parameter(name, low, high, path=..., mode="value"|"scale", log=False)`):

| `path` | Quantity |
|---|---|
| `material:NAME.E` (also `nu`, `alpha`, `rho`, `gamma`, `G`) | material, by name; with E also G changes (constant nu) |
| `section:NAME.Iy` (also `A`, `Iz`, `J`, `Asy`, `Asz`, `mass_per_length`) | beam section |
| `shell_section:NAME.t` | shell thickness |
| `plane:ID.t` | thickness of a plane element |
| `spring:ID.kx` (also `k`, `ky` ... `crz`) | spring or spring-damper |
| `elastic_support:NODE.uz` | elastic support to ground |
| `nodal_mass:NODE.mx` (also `my`, `mz`, `m`, `Ix` ...) | nodal mass |
| `node:ID.z` | node coordinate |
| `load_case:CASE` | coefficient of the case in the analyses |

`mode="scale"` reads the sample as a factor of the value in the base model;
`log=True` samples in log scale (stiffnesses spanning orders of magnitude). In
Python `apply=function(model, value)` allows any change.

### Targets

A target is an analysis plus a result, with its tolerance
(`Target(name, analysis, quantity, ..., tol=0.02)`):

| `analysis` | `quantity` | Indices |
|---|---|---|
| `{"type": "static", "cases": "G=1.35 Q=1.5"}` | `displacement`, `reaction` | `node`, `dof` |
| | `element_force` | `elem`, `comp` (N, Vy, Vz, T, My, Mz), `x` (0..1 or `maxabs`) |
| | `spring_force`, `spring_local` | `elem` (spring id), `comp` (Fx ... Mz) |
| | `shell_force`, `solid_stress` | `elem`, `comp` |
| | `max_displacement` | `dof` |
| `{"type": "modal", "n_modes": 6, "mass_source": "G=1"}` | `frequency`, `period` | `mode` (1 = first) |
| `{"type": "buckling", "n_modes": 4, "cases": ...}` | `load_factor` | `mode` |

Each distinct analysis runs once per sample, even when several targets use it.

### Algorithm

1. Initial sampling plan (Latin hypercube, Sobol or Halton,
   `scipy.stats.qmc`), by default $\max(8, (d+1)(d+2))$ samples for $d$
   parameters.
2. For each sample: copy of the model, parameters applied, analyses, results
   read (also in parallel with `n_jobs`).
3. For each target a set of regressors is tried and the one with the smallest
   **cross-validation** error is chosen (leave-one-out up to 40 samples, then
   k-fold):
   * polynomial response surface of degree 2 or 3;
   * radial basis functions (`scipy.interpolate.RBFInterpolator`);
   * kriging with anisotropic Gaussian kernel and maximum likelihood
     hyperparameters (written with numpy and scipy, no new dependency);
   * **power law** $y = c\,x_1^{a_1} x_2^{a_2}\cdots$ (log of inputs and output),
     exact for deflections, frequencies and critical loads as functions of
     moduli, inertias, thicknesses and loads;
   * the same forms on the log of the output when its sign is constant.
4. Target error: $\max_i |y_i^{cv} - y_i| / (\max y - \min y)$. When it exceeds the
   tolerance, samples far from the existing ones are added (maximin), weighted
   with the kriging uncertainty on the worst target.
5. The loop stops when all targets are reproduced faithfully or at the
   `max_samples` cap; targets that do not converge are reported (warning
   `SURROGATO_NON_CONVERGE`).

`Surrogate.validate(model, n)` compares the surrogate with feagent on new
samples; `Surrogate.sensitivity()` computes first order and total Sobol indices
(Saltelli and Jansen estimators) directly on the surrogate; a prediction
outside the ranges issues `SURROGATO_FUORI_DOMINIO`.

```python
from feagent.surrogate import Parameter, Target, build_surrogate

s = build_surrogate(
    model,
    parameters=[Parameter("E", 25e9, 40e9, path="material:CLS.E"),
                Parameter("k_bearing", 1e7, 1e9, path="spring:10.kx", log=True),
                Parameter("fQ", 0.5, 1.5, path="load_case:Q")],
    targets=[Target("deflection", {"type": "static", "cases": "G=1 Q=1"}, "displacement",
                    node=12, dof="uz", tol=0.01),
             Target("M_pier", {"type": "static", "cases": "G=1 Q=1"}, "element_force",
                    elem=40, comp="My", x=0.0, tol=0.01),
             Target("f1", {"type": "modal", "n_modes": 3, "mass_source": "G=1"}, "frequency",
                    mode=1, tol=0.005)])
print(s.summary())
s.predict({"E": 33e9, "k_bearing": 2e8, "fQ": 1.2})
s.save("surrogate.json")          # Surrogate.load("surrogate.json")
s.to_excel("surrogate.xlsx")      # fidelity, parameters, targets, samples, validation
```

A response with a kink cannot be approximated by a smooth function, and the
surrogate rightly says so. An example is the first mode switching from one
direction to the other as the inertias vary. In such cases split the targets
(one frequency per direction) or narrow the ranges.

### From Excel, the command line and the GUI

In the model workbook add the sheets `SurrogateParameter`
(`Name, Path, Low, High, Mode, Log`), `SurrogateTarget` (`Name, Analysis,
Quantity, Cases, MassCases, Modes, Node, Dof, Elem, Comp, X, Mode, Tol`) and,
optionally, `SurrogateSettings` (`Key, Value`: `method`, `n_initial`,
`max_samples`, `batch`, `sampling`, `seed`, `jobs`, `validate`).

```bash
feagent surrogate build model.xlsx -o surrogate.json --report surrogate.xlsx --jobs 4
```

```bash
feagent surrogate predict surrogate.json E=33e9 k_bearing=2e8 fQ=1.2
```

Instead of the sheets, `--spec spec.json` with `parameters`, `targets` and
`settings`. In the GUI: **Analysis > Surrogate model** opens the three tables,
builds the surrogate (tables *Surrogate fidelity*, *Validation* on new samples
and *Sensitivity* with the total Sobol indices), gives instant predictions and
saves the `.json` file.

## Reduced model

### Guyan

`guyan(model, masters, cases=None, mass_source=None)` condenses the model on
the master DOFs (whole nodes or `(node, dof)` pairs):

$$
\mathbf{u} = \mathbf{T}\,\mathbf{u}_m, \quad
\mathbf{T} = \begin{bmatrix} \mathbf{I} \\ -\mathbf{K}_{ss}^{-1}\mathbf{K}_{sm} \end{bmatrix}, \quad
\mathbf{K}_r = \mathbf{T}^{\mathsf T}\mathbf{K}\,\mathbf{T}, \quad
\mathbf{M}_r = \mathbf{T}^{\mathsf T}\mathbf{M}\,\mathbf{T}
$$

In statics it is **exact**: internal loads are reduced to the masters and their
local effect is recovered with `ReducedModel.expand`. In dynamics it is
approximate and improves with the number of masters (Guyan 1965).

### Craig-Bampton

`craig_bampton(model, boundary, n_modes=10, mass_source=None)` adds the first
fixed-interface modes to the static shapes (Craig and Bampton 1968). With
`n_modes="auto"`, `f_max` and `tol` the internal modes grow until all the
frequencies up to `f_max` are within `tol` of the full model.

### Fidelity and superelements

`ReducedModel.check(cases, n_modes)` compares statics (master displacements)
and frequencies with the full model. The reduced model goes back into another
model as a **superelement** (substructuring):

```python
from feagent.reduction import craig_bampton, guyan

pier = ...                                      # pier model with foundation
red = guyan(pier, masters=[100])                 # pier top node
red.check()                                      # {'static': {...}} zero error in statics
bridge.add_superelement(1, **red.superelement_data())
red.save("reduced_pier.npz")                     # load_superelement("reduced_pier.npz")
```

The Craig-Bampton internal modes become auxiliary nodes of the host model. The
mass of a superelement is a full matrix: modal and dynamic analyses switch to
the consistent mass automatically (warning `SUPERELEMENTO_MASSA`).

```bash
feagent reduce model.xlsx --masters 5,9 --method craig-bampton --modes auto --fmax 20 --mass-cases G=1
```

Current limits: the reduction is linear (no cables nor one-way springs) and
does not accept kinematic constraints nor non-zero settlements in the model to
reduce; superelements are not saved in the Excel file of the host model. The
reduction is used from Python and the command line, not from the GUI.

Checks (`tests/test_surrogate.py`, `tests/test_reduction.py`):

* cantilever $w = P L^3/(3EI)$ with E, I and the load coefficient as
  parameters, reproduced by the power law with error $10^{-7}$ and validated on
  new samples;
* oscillator $f = \sqrt{k/m}/(2\pi)$ within 0.5 %;
* Sobol indices: Poisson's ratio does not affect the Euler deflection;
* Guyan exact in statics with internal loads, substructure reinserted as a
  superelement equal to the full model, Craig-Bampton reinserted with the
  frequencies within 0.2 %.
