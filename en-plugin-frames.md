---
layout: default
title: "49 - Frames plugin"
parent: English
nav_order: 49
---

# 49 - Frames plugin

The `frames` plugin brings the three engines of the Telai program into feagent: **2D plane
frame**, **3D space frame of bars only** and **3D frame with an RC core**, together with the
NTC 2018 analysis (typed load cases, combinations, equivalent static earthquake, envelopes),
the timber member checks and the Word report. The calculation is **feagent only**: no
dependency on OpenSees or on the original repositories.

## How it is calculated

* **Plane frame.** A feagent model in the XY plane with `uz`, `rx`, `ry` fixed at every node,
  leaving the 3 plane-frame degrees of freedom (`ux`, `uy`, `rz`). Members are Euler Bernoulli
  beams whose local `y` axis is the member axis rotated by +90 degrees. Local distributed loads
  `qx`, `qy` vary linearly between end `i` and end `j` and are integrated exactly.
* **Space frame.** Euler Bernoulli beams with 6 degrees of freedom, using the local axes of the
  original program: `x` along the member, `y = global_z × x` (global `y` is the reference for
  columns), `z = x × y`. The local end forces equal those of the original solver (same sign
  convention).
* **Frame with core.** As above, with the walls modelled by **ShellQ4** (membrane plus Mindlin
  bending, 6 degrees of freedom per node), contributing to the stiffness of every case. The
  self weight of the walls is added to the first G1 case (`peso` column of `shell_sections`,
  in kN/m³).
* **NTC 2018 analysis.** Each elementary case is solved once on the feagent model (a single model
  carries all cases). Then, as in the original program: seismic weight `G1 + G2 + Σψ2·Qk`,
  fundamental period from feagent modal analysis (`Model.modal`, horizontal masses `W/g`,
  massless degrees of freedom condensed), SLV design spectrum with `q` and SLD elastic spectrum,
  equivalent static forces with 5 per cent accidental eccentricity, the combinations of paragraph
  2.5.3, envelopes per element group, reactions, SLD drift, SLV `d_E` and equilibrium checks. The
  seismic cases (`SLV+Xa`, `SLV-Xa`, ...) are registered as load cases of the model and the
  combinations are returned in `Result.combinations`, so the interface shows and solves them
  directly.

### Differences from the original program

| Aspect | Original program | Plugin |
|---|---|---|
| Solver | OpenSees (`elasticBeamColumn`, `ShellMITC4`) | feagent (`add_beam`, `ShellQ4`) |
| Trapezoidal distributed load, 3D | 10 point loads per member | exact integration |
| Trapezoidal distributed load, 2D | mean of the two ends | exact integration (identical for uniform loads) |
| Walls | ShellMITC4 | Mindlin ShellQ4: see the difference below |
| Interface pages (Streamlit) | yes | no: the interface is feagent's, built from the action parameters |

**Shell difference.** Measured with the original solver on the same models:

| Model | Quantity | ShellMITC4 | ShellQ4 | Difference |
|---|---|---|---|---|
| Cantilever wall 4 × 6 m, 2 × 3 elements | tip displacement (m) | 2.365e-4 | 2.442e-4 | +3.3 % |
| Cantilever wall 4 × 6 m, 4 × 6 elements | tip displacement (m) | 2.675e-4 | 2.705e-4 | +1.1 % |
| Cantilever wall 4 × 6 m, 8 × 12 elements | tip displacement (m) | 2.780e-4 | 2.790e-4 | +0.4 % |
| Example frame with core | period T1 (s) | 0.1991 | 0.2009 | +0.9 % |
| Example frame with core | maximum SLD drift (ratio to the limit) | 0.1290 | 0.1305 | +1.2 % |
| Example frame with core | SLV displacement (mm) | 22.33 | 22.50 | +0.8 % |

Both elements converge from below to the deep-beam solution (Timoshenko bending plus shear,
2.87e-4 m). On bar-only frames the results coincide with the original solver to numerical
precision: periods, drifts, reactions and member forces differ by less than 1e-9 in relative
value.

## Units

Consistent units, normally **kN, m**: `E` in kN/m², surface loads in kN/m², masses in t
(`W/g` with `g` in m/s²). No conversion is applied.

## Actions

| Action | What it does | Main outputs |
|---|---|---|
| `telaio_piano` | generates a multi-storey, multi-bay frame (or uses `modello`) and solves one case | model, summary, reactions, displacements, end and in-span forces, equilibrium |
| `telaio_spaziale` | regular bar frame (or `modello`), NTC 2018 analysis | model with all cases, `combinations`, summary, equilibrium, seismic action, modes, envelopes, reactions, drift, spectra |
| `telaio_nucleo` | as above with RC core walls | as above, plus `n_pareti` |
| `verifica_legno` | timber beam on two supports with overhangs (NTC 4.4) | summary, ULS and SLS checks, combinations, moment envelope |
| `relazione_telaio_piano` | analysis plus Word report | `.docx` file |
| `relazione_telaio_spaziale` | NTC analysis plus Word report | `.docx` file |
| `relazione_telaio_nucleo` | NTC analysis with core plus Word report | `.docx` file |
| `da_json` | accepts the JSON of the original program's `compute` | as the requested analysis |

### Plane frame parameters

| Parameter | Type | Unit | Default | Notes |
|---|---|---|---|---|
| `n_campate`, `n_piani` | integer | | 2, 3 | bays, storeys |
| `larghezza_campata`, `altezza_piano` | number | m | 5, 3.2 | |
| `E`, `A`, `I` | number | kN/m², m², m⁴ | 3e7, 0.12, 0.0036 | same section for columns and beams |
| `q_travi` | number | kN/m | −18 | uniform load, local `y`, negative downwards |
| `F_orizzontale` | number | kN | 0 | per storey, on the right-hand node |
| `caso` | integer | | 1 | load case to solve |
| `modello` | JSON | | | sheets `nodes`, `elements`, `properties`, `load_cases`, `restraints`, `node_loads`, `dist_loads` |

### Space frame and frame-with-core parameters

| Group | Parameters |
|---|---|
| Geometry | `campate_x`, `campate_y`, `altezze` (comma-separated lists, m) |
| Sections | `pilastro_b`, `pilastro_h`, `trave_b`, `trave_h` (m), `E` (kN/m²), `peso_specifico` (kN/m³) |
| Loads | `g1_solaio`, `g2`, `q`, `neve`, `vento` (kN/m²), `categoria_q` (A...H), `orditura` (X or Y) |
| Core | `posizione_nucleo`, `spessore_parete`, `E_parete`, `peso_parete` |
| Seismic | `sisma`, `suolo`, `topo`, `q_comportamento`, `ag_SLV`, `F0_SLV`, `TCs_SLV`, `ag_SLD`, `F0_SLD`, `TCs_SLD`, `metodo_T1`, `rigidezza_fessurata`, `eccentricita`, `coeff_drift`, `n_modi` |
| Combinations | `G2_compiutamente_definiti` |
| Advanced | `parametri` (JSON: any field of `ParametriNTC`), `modello` (JSON: the sheets), `parametri_legno` (JSON) |

The generator builds a regular RC frame: slab surface loads become line loads on the beams of the
chosen `orditura` (half spacing tributary width per side), the `Q` case acts on every floor except
the last, `neve` on the roof, `vento` as nodal forces on the windward faces in both directions.
With `modello` any frame described by the original program's sheets can be analysed (`nodes`,
`elements`, `properties`, `load_cases` with the columns `tipo`, `categoria`, `gruppo`,
`restraints`, `node_loads`, `dist_loads`; for the core `beam_elements`, `beam_properties`,
`shell_elements`, `shell_sections`, `beam_dist_loads`). If `properties` has a `materiale` column
with an EN 338 or EN 14080 class, those members are also checked as timber (NTC 4.4) and the
`Legno ...` tables are added to the result.

### The returned model

`Result.model` is the feagent model with all load cases (elementary and seismic) and
`Result.combinations` holds the ULS, characteristic, frequent and quasi-permanent SLS, SLV and
SLD combinations as `{case: factor}` dictionaries. The interface couples them to the model; from
the library use `result.model.solve(cases=result.combinations["SLU_001"])`.

## Examples

```python
from feagent import plugins
p = plugins.get("frames")

# 3 x 4 plane frame with storey horizontal forces
r = p.run("telaio_piano", n_campate=3, n_piani=4, F_orizzontale=25)
print(r.data["M_max_abs"], r.data["reazione_risultante_Rx"])

# space frame with SLV and SLD earthquake
r = p.run("telaio_spaziale", campate_x="5, 5", campate_y="4, 4", altezze="3.2, 3.2, 3.2",
          neve=1.2, vento=1.0, ag_SLV=0.15)
print(r.message)                         # periods and number of combinations
summary = r.tables[0]                    # analysis summary table
res = r.model.solve(cases=r.combinations["SLV_001"])

# frame with core, Word report
p.run("relazione_telaio_nucleo", campate_x="4, 4, 4", campate_y="4, 4, 4", altezze="3.5, 3.2, 3.2",
      file="relazione_nucleo.docx")

# timber beam
r = p.run("verifica_legno", classe="C24", b=0.10, h=0.20, L=4.0, neve=1.2, g2=1.0)
print(r.data["eta_SLU"], r.data["eta_SLE"])
```

From the command line:

```bash
feagent plugin run frames telaio_piano --param n_piani=4 --param F_orizzontale=25
feagent plugin run frames telaio_spaziale --param campate_x="5, 5" --param altezze="3.2, 3.2"
```

## Compatibility: the `da_json` action

Anyone already using the original program's API can pass the same JSON. Two forms:

```python
# (1) static analysis of one load case: the "telaio" field picks the engine
p.run("da_json", payload={"telaio": "piano2d", "load_case_id": 1, "nodes": [...], "elements": [...],
                          "properties": [...], "load_cases": [...], "restraints": [...], "node_loads": [...]})
# (2) a specific function
p.run("da_json", payload={"funzione": "analisi_ntc18", "nodes": [...], ..., "parametri": {"sisma": True}})
```

Values of `telaio`: `piano2d`, `spaziale3d`, `nucleo` (`Telaio2D`, `telaio3D`, `TelaioNucleo` are
accepted too). Values of `funzione`: `analisi_ntc18`, `analisi_ntc18_nucleo`, `spettro_ntc18`,
`combinazioni_ntc18`, `verifica_trave_legno`, `verifiche_legno_telaio`. If the requested case has no
rows in `restraints`, the static analysis uses the restraints of the first case that has them and
reports it among the warnings. The fields `trapezoid_segments`, `geom_transf`, `shell_element_type`
and `directory_output` are ignored: the trapezoidal load is exact and files are written by the
report actions.

## What is ported and what is not

Ported as it was: spectrum, combinations, equivalent static earthquake, envelopes (`ntc18`), timber
checks and material tables (`legno`), the frame generator, the examples, XLSX reading and writing, the
Word report (rewritten in the company style and independent of the solver). Only the layer that called
OpenSees was replaced. Not ported: the Streamlit pages (`app.py`, `ui_ntc.py`, `ui_legno.py`), because the
interface is feagent's, the HTTP server and the API envelope, the plotly plane-frame figure (the report
has an equivalent figure) and the nodal masses of the `masses` sheet (they did not affect the static
analysis of the original program). There is no rigid floor option: as in the original program every node
keeps its own masses and displacements.

## Verification

The tests (`tests/test_plugin_frames.py`) compare the plugin with closed-form solutions (3D cantilever in
bending, torsion and axial load; simply supported beam under uniform and trapezoidal load; portal frame
with the joint-rotation formula), with the original program's JSON benchmarks, with fixed values computed
with the original solver (example frame, example core and cantilever walls) and with global equilibrium
(reactions equal to the loads in every case, base shear equal to the seismic force). The parts ported as
they were carry their own tests (spectrum, combinations, timber).
