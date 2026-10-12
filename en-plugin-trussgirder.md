---
layout: default
title: "50 - TrussGirder plugin"
parent: English
nav_order: 50
---

# 50 - TrussGirder plugin

The `trussgirder` plugin brings the functions of the Reticolare program into feagent:
parametric generation of planar trusses, linear static analysis, modal analysis with
nodal masses, incremental pushover, import and export of the Excel template, and a Word
report. All calculations use **feagent only**: no OpenSees and no dependency on the
original repositories.

## How it is computed

* Each member is a truss element (`Model.add_truss`) with axial stiffness `EA/L`; the
  truss lies in the XY plane and the out-of-plane degrees of freedom (`uz`, `rx`, `ry`,
  `rz`) are fixed at every node. The `restraints` sheet acts on `ux` and `uy`.
* **Static**: `Model.solve`. Members carry axial force `N` only (positive in tension),
  so the per-member results are `N`, length, `sigma = N/A` and the regime.
* **Modal**: `Model.modal`, with the lumped mass vector taken from the `node_masses`
  sheet (`mx` along X, `my` along Y). Massless degrees of freedom are condensed by
  feagent. Frequencies, periods, mode shapes and participating masses are returned.
* **Pushover**: a material-nonlinear solver written on top of feagent's assembly (file
  `pushover.py` of the plugin). Linear geometry (small displacements), uniaxial member
  law with return mapping. Each step is solved with tangent Newton Raphson, under
  displacement control (the load multiplier is unknown) or force control (imposed
  multiplier). If a step does not converge, for example at a collapse mechanism, the
  analysis stops and returns the completed steps.

### Member constitutive law

| Law | Description |
|---|---|
| `Elastic` | linear, modulus `E` |
| `Steel01` | bilinear, yield `Fy`, post-yield slope `b*E`, kinematic hardening, elastic unloading with slope `E` |
| `ElasticPP` | elastic perfectly plastic, yield at `Fy` (or at `eps_y*E`) |

## Units

Action parameters are in **kN, m**, `E` and `fy` in MPa (converted to kN/m^2 in the
sheets), masses in t. Sheets read from XLSX or passed as `modello` are taken as they
are: units must be mutually consistent (for example kN, m, `E` in kN/m^2, masses in t).

## Actions

Every action receives the model in one of three ways, in this order of precedence:
`file` (XLSX workbook), `modello` (the sheets as a JSON object, as returned by `genera`
in `data["sheets"]`) or the generator parameters.

| Action | What it does | Main outputs |
|---|---|---|
| `genera` | generates the truss with loads, supports and masses | model with load cases, template sheets |
| `statica` | linear static analysis | `N` table per member, reactions, displacements, equilibrium, chart of `N` along the chords |
| `modale` | modal analysis with nodal masses | frequencies, periods, participating masses, `nodes_masses`, shapes |
| `pushover` | displacement or force controlled pushover | capacity curve (chart), stages, yielded members, final `N` |
| `importa_xlsx` | reads the Excel template | model and sheets |
| `esporta_xlsx` | writes input and results to an XLSX | `.xlsx` file |
| `relazione` | Word calculation report | `.docx` file |

### Model parameters (shared)

| Parameter | Type | Unit | Default | Notes |
|---|---|---|---|---|
| `tipologia` | choice | | `Warren` | Warren, Howe, Pratt, Mohniè (K), Nielsen, Parabolica, Parabolica rovescia, Diagonale doppia |
| `luce` | number | m | 20 | span |
| `altezza` | number | m | 2.5 | at mid span for the parabolic types |
| `n_pannelli` | integer | | 8 | Warren needs at least 4 panels |
| `area_correnti` | number | m^2 | 0.01 | chords |
| `area_anima` | number | m^2 | 0.005 | verticals and diagonals |
| `E` | number | MPa | 210000 | |
| `legame` | choice | | `Elastic` | `Steel01`, `ElasticPP` for pushover |
| `fy` | number | MPa | 355 | nonlinear laws only |
| `incrudimento` | number | | 0 | `b` of Steel01 |
| `massa_nodale` | number | t | 1 | `node_masses` sheet |
| `carico_tipo` | choice | | top chord mid node | or all top or all bottom chord nodes |
| `carico_fy`, `carico_fx` | number | kN | -10, 0 | per node, positive along the axes |
| `carichi` | table | kN | | rows `node_id, fx, fy`: replace the generated loads |
| `caso_di_carico` | integer | | 1 | |
| `file`, `modello` | file, JSON | | | alternative sources to the generator |

Action specific parameters: `modale` has `n_modi` and `massa_default`; `pushover` has
`controllo` (`spostamento` or `forza`), `nodo_controllo` (0 = most loaded node),
`gdl_controllo` (`uy` or `ux`), `spostamento_finale` (m, signed), `fattore_finale` (load
multiplier) and `passi`. Analysis actions accept `xlsx_out` to also write the XLSX;
`esporta_xlsx` and `relazione` have `analisi` (`statica`, `statica+modale`,
`statica+pushover`, `statica+modale+pushover`) and `file_out`.

## Examples

From the library:

```python
from feagent import plugins
p = plugins.get("trussgirder")

g = p.run("genera", tipologia="Pratt", luce=10, altezza=2, n_pannelli=6)
s = p.run("statica", modello=g.data["sheets"])
print(s.data["N_max_trazione"], s.data["N_max_compressione"])

m = p.run("modale", tipologia="Pratt", luce=30, altezza=3, n_pannelli=12, n_modi=4)
print(m.data["frequenze_Hz"])

po = p.run("pushover", tipologia="Pratt", luce=10, altezza=2, n_pannelli=6,
           controllo="forza", fattore_finale=4.0, passi=40)
print(po.data["aste_snervate"], po.data["lambda_finale"])

p.run("relazione", tipologia="Howe", luce=20, altezza=2.5, n_pannelli=8,
      analisi="statica+modale", file_out="report.docx")
```

From the command line:

```bash
feagent plugin run trussgirder statica --param tipologia=Pratt --param luce=10 --param altezza=2 --param n_pannelli=6
feagent plugin run trussgirder esporta_xlsx --param file=model.xlsx --param analisi=statica+modale --param file_out=results.xlsx
```

## Excel template

The workbook has seven sheets with the same columns as the original program template
(`template_input_truss2d.xlsx`, which ships empty):

| Sheet | Columns |
|---|---|
| `nodes` | `id, x, y, chord` |
| `elements` | `id, n1, n2, A, matTag, group` |
| `materials` | `matTag, type, E, Fy, b` |
| `load_cases` | `id, name` |
| `restraints` | `load_case_id, node_id, ux, uy` (1 = restrained) |
| `node_loads` | `load_case_id, node_id, fx, fy` |
| `node_masses` | `node_id, mx, my` |

`esporta_xlsx` adds the result sheets `results_nodal`, `results_elements`,
`modal_frequencies`, `modal_shapes`, `pushover_curve`, `pushover_elements_final`,
`pushover_elements_history`.

## What was ported and what was not

* **Ported**: generator and truss types, statics, modal analysis with nodal masses,
  displacement and force controlled pushover with the same constitutive law, Excel
  template, Word report (rewritten in the company style, without naming tools).
* **Not ported**: the Streamlit page (`app.py`) and the HTTP endpoints, since the
  interface is the feagent GUI, which builds the form from the declared parameters.
* **Generator defect fixed**: in the Parabolica type the top chord touches the bottom
  chord at the supports, and the original program left two distinct nodes there,
  joined only by a zero length vertical (discarded): the truss was a mechanism with two
  degrees of freedom (displacements around 1e14, hidden by the original solver). The two
  ends are now a single node and the scheme is statically determinate. The other types
  are unchanged.

## Validation

The tests (`tests/test_plugin_trussgirder.py`) compare:

* the **method of joints** (joint equilibrium solved separately) with the member axial
  forces for Warren, Howe, Pratt and Parabolica, and global equilibrium for every type;
* closed form solutions: two bar frame, triangle with the virtual work displacement, a
  single bar with a mass, collapse load of a determinate truss;
* the numbers of the original program (one off check with OpenSees): `N` and
  displacements for seven types (relative difference below 1e-6), the first four
  frequencies of the Pratt truss (1e-6), Steel01 pushover in displacement and force
  control (1e-7) and ElasticPP in displacement control (1e-7);
* the frequency of a Warren truss with L = 30 m and H = 3 m against the equivalent
  Timoshenko beam (bending stiffness of the chords, shear stiffness of the diagonals):
  difference about 1 %, tolerance 3 %. The Euler Bernoulli beam without shear
  deformability overestimates the frequency by about 14 %.

Note on force controlled ElasticPP pushover: exactly at the collapse load the original
program stops one step earlier (it does not converge with a zero tangent), while here
the step with members exactly at yield converges; the limit load is the same.
