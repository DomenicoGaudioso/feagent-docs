---
layout: default
title: "DigitalTwin plugin"
parent: English
nav_order: 51
---

# DigitalTwin plugin (digital twin)

The `digitaltwin` plugin is the feagent port of **femDTO**: it compares an FEM model with the
experimental data of a structure (OMA frequencies, mode shapes and damping, static tests) and updates
its parameters (model updating). The solver is always feagent: no OpenSees dependency. The Streamlit
front end of the original is replaced by the feagent GUI (Plugin menu), which builds its forms from the
action parameters; the same actions are available from the library, the CLI and the AI tools.

```python
from feagent.plugins import get
dt = get("digitaltwin")
r = dt.run("calibra", modello="bridge.xlsx", obiettivo="frequenze",
           parametri=[{"gruppo": "section", "tag": "1", "parametro": "E", "min": 1.5e11, "max": 2.6e11}],
           frequenze_sper=[{"f_Hz": 3.2, "peso": 1}], metodo="DE", max_eval=500, seme=42)
r.model          # updated feagent model
r.tables[0]      # calibrated parameters
```

Optional extras: `scipy`, `pandas`, `openpyxl`, `pydantic`; `pymoo` and `optuna` only for NSGA-II,
CMA-ES, PSO and TPE.

## Input models

| Source | Sheets or keys | Notes |
|---|---|---|
| DTO JSON (`dictOPS`) | `StructuralAnalysisModel` | also the OpenSees `printModel` JSON; missing supports are added with the `vincoli` parameter |
| `ops` Excel | `Point` or `Nodes`, `BeamEle`, `AreaEle`, `Material`, `Section`, `Supports`, `Rigid Link`, `Beam Load`, `Node Load` | femDTO format |
| MIDAS-like Excel | `Nodes`, `Material`, `Section`, `Elements`, `Supports`, `Elastic Link`, `Beam End Release`, `Rigid Link`, `Beam Load` | file units, density from the `Mass Density` column |
| SAP2000-like Excel | `Joint Coordinates`, `Connectivity - Frame`, `Frame Props 01 - General`, restraints and joint loads | frames only; areas and links are not imported |
| feagent workbook (`formato = feagent`, recognised in `auto`) | `Node`, `Element`, `Material`, `Section`, `Support`, loads, `Combination`, `Units` | the one written by `Model.to_excel` or by the GUI, for instance the model of the `bridge` plugin; no conversion: the model stays feagent |
| `feagent.Model` | object passed as `modello` from Python | units N, m; the original model is never modified |

Beam local axes follow OpenSees (`vecInLocXZPlane` is the local z axis; `Iy` about y, `Iz` about z).
The Excel readers put z upwards (as Midas and the example JSON do), or y upwards for SAP2000;
`orientamento = terna` reproduces the Gram-Schmidt triad of the original. Mapping: `ElasticBeam3d` to an
Euler-Bernoulli beam, `ForceBeamColumn3d` to a beam with elastic section (Timoshenko when `Avy`/`Avz` are
given), `ShellDKGQ` to Q4 or T3 shells, `ZeroLength` and `TwoNodeLink` to elastic ground supports or rigid
ties, `rigidLink` to `add_rigid_link`, `massperlength` to the section line mass. A labile structure is
stabilised with very weak ground springs and reported in the messages.

MIDAS elastic links of type GEN are imported as rigid ties on the flagged degrees of freedom (`RIGID`
column) in global axes; the link angle and finite-stiffness springs between free nodes are not
represented and are reported.

## Actions

| Action | What it does |
|---|---|
| `importa_modello` | reads JSON or Excel, builds the model, summarises elements and load patterns, saves the `dictOPS` |
| `modale` | frequencies, periods and effective masses |
| `confronto_sperimentale` | measured vs computed frequencies, deviations, MAC, mode pairing (Hungarian algorithm) |
| `sensibilita` | Sobol indices `S1` and `ST` (and `S2`) of the first frequencies against the parameters |
| `calibra` | model updating on frequencies, OMA (frequencies and shapes) or static displacements; the updated model is `Result.model` |
| `statica` | load pattern combination: displacements, reactions, peak member forces |
| `buckling` | critical multipliers under a static condition: load cases with factors and, for feagent models, the stopped train |
| `treno_statico` | feagent grillage models: train stopped (or at the worst position) on a main girder, added to the model as a load case |
| `passaggio_treno` | railway train at varying speed, Newmark with Rayleigh damping, multiple tracks (dictOPS and beam models; not for feagent grillages) |
| `scenari_danno` | element removal or parameter degradation, scenario frequencies |
| `monitoraggio` | FFT, Welch PSD, band-pass filter, peaks and half-power damping from accelerometers |

### Parameters to calibrate

Table with columns `gruppo`, `tag`, `parametro`, `min`, `max`. For dictOPS models the groups are: `material`, `section`, `beamEle`,
`node` (nodal mass), `nodeLoad`, `beamLoad`; tags are ids or ranges (`1,3,10:20`). Values are absolute, or
multipliers with `limiti = relativi`. A section change propagates to the elements using it; a material
change to the dependent sections and elements (Poisson's ratio is kept, so G changes too).


### feagent models: parameters by name

For a feagent workbook or `Model` the groups refer to entities **by name**, the one in the `Material` and
`Section` sheets (unnamed: `MAT<n>`, `SEC<n>`). The `Materiali` and `Sezioni` tables of `importa_modello`
list names, properties, element counts and element ids.

| gruppo | tag | parametro |
|---|---|---|
| `materiale` | material name or ordinal (`Acciao`, `C.a.`) | `E`, `G`, `nu`, `rho`, `alpha` |
| `sezione` | section name, wildcards allowed (`Soletta`, `Cordolo*`, `C0?`) | `A`, `Iy`, `Iz`, `J`, `Asy`, `Asz`, `mass_per_length`; also `E`, `G`, `nu`, `rho` on the material of the elements of that section only |
| `elemento` | element ids, `1:200,305` (beams, trusses, shells) | as `sezione` |
| `molla` | nodes with a ground spring (parameter `ux`..`rz`), or a spring id (parameter `k`) | stiffness |
| `massa` | load case that generates mass (must be in `massa_da_carichi`) | `fattore` |

The model being calibrated is never modified: evaluations work on a working copy with the new values (material
or section copied only for the elements concerned) that is restored before the next evaluation, so the model
is not copied at every step. Changing `E` or `nu` keeps `G = E / (2 (1 + nu))` when
the material was isotropic. The updated model (`Result.model`) keeps nodes, supports, springs, loads, cases,
combinations and the `Units` sheet (`Result.units`); `salva_json` ending in `.xlsx` writes it as a workbook.
Mass is the one given by the density `rho` plus the cases listed in `massa_da_carichi`: for the `bridge`
plugin model use `G2` only, since the self weight `G1` is already in the element density. The length unit of
the `Units` sheet sets the gravity used to turn load cases into mass.

#### Static train and buckling on the grillage bridge

The dynamic passage needs a chain of beams and is **not provided for a feagent grillage** (longitudinal
girders, cross beams, slab strips): `passaggio_treno` on a `bridge` plugin model answers with a message pointing
to `treno_statico`. This action places the train (plugin catalogue: `ETR500`, `Modello1`, ...; `feagent.vehicles`
catalogue: `LM1_TS1`, `LM71`, `HL93_TRUCK`, ...; or `personalizzato` with an `interasse_m`, `peso_kN` table) on the
chosen **longitudinal line** and adds it to the model as load case `TRENO`, as point loads on the line elements.

* `linea`: empty = a main girder (among the longitudinal elements forming a chain at least half as long as the
  model, the section with the greatest bending stiffness, `Trave`, and among its chains the one with the lowest
  transverse coordinate). To choose another: section name (wildcards allowed), `y=3` (the chain nearest to that
  transverse coordinate), `n=2` (the second in order of coordinate), separated by `;`. The line ends must be
  restrained nodes, otherwise a warning is issued.
* `posizione`: abscissa of the first axle along the line. Empty or `critica`: the position maximising the vertical
  displacement of the model, found with a static moving-load scan (at most 400 positions, then 41 refined ones).
* The result reports the maximum displacement of the applied case and that of the static envelope of a finer,
  independent scan; `Result.model` holds the `TRENO` case, which is passed as `modello` to `statica`, `buckling`
  and `calibra` (static targets), or saved with `salva_json`. `statica` and `buckling` also take `convoglio`,
  `linea`, `posizione` directly.

`buckling` works on a static condition: `carichi` (model cases with factors, e.g. `G1 + G2`) and, if given, the
stopped train. A simply supported bridge has no compression under its weights or under the vertical train: there
is no positive multiplier and the result says so with a warning (not an error); a huge multiplier (above 1000) is
flagged in the same way. To study instability add a case with axial compression.

| Test | Reference | Measured |
|---|---|---|
| Euler column pinned-pinned, 6 m, EI = 84 MN m2 | P_cr = pi2 EI / L2 = 23,029.08 kN | 23,029.23 kN (0.0007 %) |
| fixed-pinned column | effective length 0.6992 L | within 0.02 % |
| 30 m bridge, G1 + G2 | no compression | no multiplier, warning |
| 30 m bridge, 1 MN compression at each end of the two girders | | lambda_cr = 68.18 (136 MN in total), vertical train added: unchanged |
| `LM1_TS1` train on girder `Trave` (y = -3), critical position, head at 15.6 m | static envelope of `moving_load_analysis` (300 positions) | 0.054370 m against 0.054369 m (0.001 %) |
| `LM71` train, head at 17.4 m | same | 0.088946 m against 0.088945 m (0.001 %) |
| one 100 kN axle on a 30 m simple beam | P L3 / (48 E I) | error below 0.2 % |

Timings on the single-span bridge (shared machine): `treno_statico` 4 s for `LM1_TS1` (415 positions solved),
4 s for `LM71`, 7 s for `ETR500` (941 positions).

#### Mass counted twice

If the model has the density `rho` and a case in `massa_da_carichi` has a vertical resultant equal to the self
weight from `rho` (within 5 %, with a warning also up to 20 % difference), the mass is practically doubled. Every
action using `massa_da_carichi` then issues a warning with the self mass, the case mass, the ratio, the resulting
total mass and how much too low the frequencies come out. `escludi_peso_proprio` (default false) automatically
excludes those cases from the mass. On the 30 m bridge: `G2` alone (54 t against 394 t of self mass) gives no
warning, f2 1.950 Hz; `G1 + G2` gives the warning (G1 = 337 t, self mass from `rho` 342 t, ratio 0.985) and f2
drops to 1.424 Hz (-27 %); with `escludi_peso_proprio` it returns to 1.950 Hz. In the twin-girder and grillage
decks the mass from `rho` matches `G1` within 1.5 % once the kerb strip width is corrected (station spacing, not the
kerb length). In the box and RC decks the slab between the girders has no elements: its weight lives only in `G1`
and the mass from `rho` is lower (13 % and 6 %), so `escludi_peso_proprio` would remove real mass: decide the mass
case by case for those bridges.

#### Checks on the single-span bridge (30 m)

Measured with `massa_da_carichi = G2`. The absolute frequencies in this section (1.483, 1.809, 4.065, 4.092 Hz) were measured before the kerb strip correction; they are now 1.689, 1.950, 4.350, 5.002 Hz. Relative comparisons (parameter recovery, errors, Sobol indices) are unchanged and covered by tests.

| Action | Test | Outcome |
|---|---|---|
| `sensibilita` | `E` of steel, of `Soletta` and of `Cordolo` (measured before the kerbs became the same section as the slab; the test now uses `Traverso`), N = 32 (160 analyses, 8.7 s) | ST of f1: steel 0.872, slab 0.142, kerbs 4e-7; ST of f3: 0.278, 0.812, 2.5e-5. With N = 32 some S1 exceed 1 (1.036): estimator noise |
| `calibra` static | targets: midspan displacements with steel `E` +10 %, case `G1 + G2` | recovered +10.005 % (105 evaluations) |
| `calibra` static | same targets with `Soletta` `E` +10 % | **not identifiable**: midspan displacements do not change (relative change 1e-9); the calibrated value is arbitrary and there is now a warning |
| `calibra` OMA | frequencies and shapes of 4 visible modes at 12 vertical sensors (girders and kerbs), model with steel +6 % and slab +10 % | steel +5.992 %, slab +9.956 %; MAC 1.0000 on the 4 modes; frequency errors below 0.02 %; 420 evaluations, 40 s |
| `scenari_danno` | removal of one girder element at midspan | f1 from 1.483 to 0.550 Hz (-63 %) |
| `scenari_danno` | removal of all girders at midspan | structure split, zero frequencies: warning |
| `scenari_danno` | `Iy` of the girders degraded up to -30 % | f2 -15.5 % (the square root of 70 % would give -16.3 %) |
| `monitoraggio` | synthetic accelerometer at 50 Hz, 900 s, first 4 frequencies with equal-amplitude white noise | peaks 1.4832, 1.8066, 4.0649, 4.0894 Hz against 1.4829, 1.8092, 4.0654, 4.0922 Hz (errors 0.02, 0.14, 0.01, 0.07 %) |

Defects found and fixed: static calibration now warns when a parameter is not identifiable (the objective does
not change between the two bounds); the OMA comparison warns when the sensors read less than 5 % of the maximum
displacement of a mode (transverse modes are invisible to vertical sensors in the bridge: the shape is normalised
noise and the MAC is meaningless); scenarios with zero frequencies (labile structure) are flagged; the static
analysis of a 1000-dof grillage used a dense LU (0.8 s per evaluation, 0.04 s with the sparse solver).

#### Test case: two-girder composite bridge, 30 + 40 + 30 m

The bridge generated by the `bridge` plugin (`misto_2travi`, 606 nodes, 768 beams, 3636 degrees of freedom,
materials `Acciao` and `C.a.`, sections `Trave`, `Soletta`, `Sbalzo`, `Traverso`; the kerbs share the slab section and are told apart by element id only) is calibrated with
`massa_da_carichi = G2`. Nominal frequencies of the first six modes: 1.1344, 1.3676, 1.7304, 1.9897, 2.1693,
2.5387 Hz. The "measured" frequencies come from the same model with one parameter increased by 10 %, and
the calibration starts from the nominal model (DE, seed 42, `max_eval` 600, bounds 170 and 250 GPa for steel, 27 and 41 GPa for the slab):

| Perturbed parameter | Measured frequencies [Hz] | Recovered value | Error on the +10 % | Evaluations | Time |
|---|---|---|---|---|---|
| steel `E` (210 GPa) | 1.1739, 1.4321, 1.7956, 2.0683, 2.2635, 2.6410 | 231.000 GPa | +9.999998 % | 240 | 33 s |
| `E` of section `Soletta` (34.077 GPa) | 1.1505, 1.3697, 1.7498, 2.0080, 2.1793, 2.5571 | 37.4847 GPa | +9.99999 % | 225 | 36 s |
| both, calibrated together | 1.1897, 1.4344, 1.8148, 2.0867, 2.2751, 2.6625 | 231.005 and 37.482 GPa | +10.003 % and +9.991 % | 630 | 119 s before, 65 s with the restored working copy |

Residual frequency errors are below 0.001 %. The restored working copy instead of a per-evaluation `deepcopy`
gave the same parameters (bit-identical) and the same objective (difference 3e-18); parallel evaluations
(scipy `workers`) are not used because the objective is a non-picklable closure and on Windows process cost
exceeds the gain. One evaluation (matrices, sparse modal analysis) costs about 0.10 s (previously 0.19 s); the test machine was shared. Sobol sensitivity (N = 32, 160 modal
analyses, 30 s), total indices ST on the first five frequencies: steel 0.85 1.03 0.93 0.96 1.01, `Soletta`
0.16 0.00 0.09 0.06 0.02, `Cordolo` 0.00: kerb stiffness cannot be identified from frequencies and the
slab only weakly.
### Experimental data

* `frequenze_sper`: `f_Hz`, `peso`, `xi_%` (damping is only reported in the comparison);
* `oma`: one row per mode and sensor: `modo`, `f_Hz`, `nodo`, `dir` (x, y, z), `valore`; or `oma_file`
  (femDTO Excel: frequencies in the first row, one sensor per row, one mode per column) with `corr_file`
  (CSV `sensorID;femID`);
* `statica_bersagli`: `nodo`, `gdl` (1 to 6), `target`, `peso`, or `statica_file` (sheet `nodeDisp`).

### Objective functions

Frequencies: $E=\sum_j w_j\left(\frac{f_{exp,j}-f_{FEM,i(j)}}{f_{exp,j}}\right)^2$. OMA:
$E=\sum_j \alpha_j\,\Delta f_j^2+\beta_j\,(1-MAC_{jj})$. Static: $E=\sum w\,((u-u_t)/u_t)^2$. Modes are
paired with the Hungarian algorithm on the cost matrix. Compared with femDTO the cost matrix is no longer
rounded to 5 decimals, the weights follow the experimental mode, and an unmatched experimental mode costs
its weight.

### Optimisation methods

`DE`, `DualAnnealing`, `DIRECT`, `Powell`, `Nelder-Mead` (scipy), `NSGA-II`, `CMA-ES`, `PSO` (pymoo),
`TPE` (optuna), with fixed seed and convergence history. A missing library produces a clear error.

## Ported and not ported

Ported: DTO schema, Excel and JSON readers, MAC and modal comparison, objective functions, optimisation
dispatcher, Sobol sensitivity (Saltelli and Jansen estimators on a scipy Sobol sequence, instead of
SALib), train passage, damage scenarios, filters and spectra. Not ported: the Streamlit interface and the
pyvista plots (visualisation is the feagent GUI), the white-noise ambient analysis and the earthquake
analysis with accelerograms (feagent already has time-history analyses), the videos.

## Validation

The tests (`tests/test_plugin_digitaltwin.py`) compare frequencies of simply supported and cantilever
beams with closed forms (deviation below 0.3 %), the Euler load (0.5 %), the midspan deflection, a slow
train passage with the envelope of static deflections (3 %), Sobol indices of the Ishigami and linear
functions, and the recovery of known elastic moduli from frequencies generated by the model. A one-off
check against OpenSees on a 20-element simply supported beam gives the same first four frequencies
(0.90272 and 3.61085 Hz) to eight digits.
