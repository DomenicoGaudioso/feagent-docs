---
layout: default
title: "47 - Bridge plugin"
parent: English
nav_order: 47
---

# 47 - Bridge plugin

The `bridge` plugin builds the **planar grillage model of a bridge deck** directly as
a `feagent.Model`: main girders, cross-beams, slab strips, kerbs, bearings, sections,
phased composite sections, Gerber saddles and permanent loads. The calculation is **feagent
only**: no dependency on OpenSees or on the original repositories.

The modelling part is ported from
[BridgeBIM](https://github.com/DomenicoGaudioso/BridgeBIM) (the four `src_fem.py` files of
the deck types, `assi_travi.py` and the default parameters). Nothing from BridgeBIM about IFC,
CAD, Tekla, Revit or bills of quantities is ported: only the analytical model is needed, which
the original wrote to an Excel file for MIDAS and which here becomes a feagent model ready to
solve.

## Deck types

| `tipologia` | Deck | Notes |
|---|---|---|
| `misto_2travi` | steel-concrete composite, two girders | optional spine stringer, optional diaphragm bracing, phased composite sections |
| `misto_graticcio` | steel-concrete composite, `n_travi` girders | same options; with `n_travi = 2` it equals `misto_2travi` |
| `misto_cassone` | steel-concrete composite box | two webs, axis from the set-back edges (offsets) |
| `ca_rettangolare` | reinforced concrete, rectangular girders | `nt` girders at constant spacing, concrete cross-beams, Gerber saddles as hinges |

## How the model is built

The grillage is **planar**: all nodes lie at `z = 0` and the bridge axis follows the `As`
sheet. At each **station** along the axis (concrete segment ends, cross-beams, bearings, plus
intermediate points at a maximum spacing `passo_max_trasv`) the nodes of the cross section are
created, left to right:

`left kerb tip`, `left edge`, `left girder` (with any intermediate girders), `right girder`,
`right edge`, `right kerb tip`.

All elements are feagent beams:

* **longitudinal girders**, one section per segment (steel: welded I section from the plates
  `psup`, `sa`, `pinf`; concrete: rectangle `B x H` with `H` linear along the segment);
* **cross-beams** at cross-beam stations, **slab strips** elsewhere (and under the cross-beams
  too in the two-girder and grillage composite types), **cantilevers** between edge and outer
  girder, **kerbs** between tip and edge;
* slab strips have section `spacing between stations x thickness`: they represent the slab in
  the transverse distribution;
* **spine stringer** (`con_longherina`) and diaphragm and plan **bracing** (`con_controventi`,
  low nodes and rigid links) in the two-girder and grillage types;
* **Gerber saddles** in concrete: one flexural hinge (release of `My`) per girder at each joint,
  on a single end so that the segment does not become a mechanism.

Supports are those of the original: vertical on all bearing lines, transverse and longitudinal
restraints that leave free expansion (concrete: a single longitudinal fixed point on the whole
bridge; composite: one longitudinal restraint at each of two different bearings).

### Phased composite sections

With `con_fasi` the `Section` sheet also carries the phase rows (column `group`, WOBridge
scheme): `group 0` steel girder, `1` composite long term with `fasi_nL`, `2` shrinkage, `3`
settlements, `4` short term with `fasi_n0`. The effective slab width is `fasi_beff` (m) and the
thickness `fasi_tslab` (m); with `fasi_crack` (m) the sections within that distance from the
inner bearings are cracked (steel plus reinforcement `fasi_fi` at spacing `fasi_passo`). In
feagent they become the section groups `group1` ... `group4` and the load case selects the group
by name (`G1` on the steel girder alone, `G2` on the long-term section), as in WOBridge.

## Conventions kept from the original

* global axes: `x` longitudinal along the bridge axis, `y` transverse with the left edge at
  `y < 0` and the right one at `y > 0`, `z` vertical; the grillage is planar (`z = 0`);
* the **parameter** lengths are in mm (`spessore_soletta`, `larghezza_cordolo`, `offset_sx`,
  ...), span lengths and the `As` and `At` sheets in m;
* section properties are the original ones: torsion of the rectangle as Saint Venant constant,
  shear area `Asz` to the web and `Asy` to the flanges (local `z` vertical), vertical bending
  with `Iyy`, releases on `My`;
* the local axes orientation is the WOBridge one (`geomTransf Linear` with the `ey` vector as
  `vecxz`), the same as the `feagent.io_wobridge` reader;
* the girder axis rule is the one of `assi_travi`: from the `At` sheet for the two-girder and
  grillage types, from the edges `P1`/`P4` set back by the offsets for the box, at spacing `int`
  for concrete;
* node and element numbers are those of the original, step by step.

### Where the plugin departs from the original

| Point | Original | Plugin |
|---|---|---|
| Units | kN and m (`E` in kN/m²) | **SI, N and m** (`E` in N/m²), because a `feagent.Model` carries no unit and the GUI reads N and m |
| Loads | none: only geometry, sections and supports | cases `G1` and `G2` (see below) |
| Box, slab strips | as wide as the transverse span (about eight times too stiff) | as wide as the spacing between stations, like the other types (`strisce_tributarie`, true by default; false reproduces the original) |
| Concrete with an odd number of girders | `assi_travi.offsets_interasse` wrote `-nt // 2`, which is `(-nt) // 2`: the girders ended up shifted by one spacing (3 girders at `-2i, -i, 0`) | `-(nt // 2)`: 3 girders at `-i, 0, +i` |
| Density | `Density` column in kN/m³ | `gamma` in N/m³ and `rho` from the `Mass` column, for modal analysis |

For everything else the tables produced coincide cell by cell with those of the original
generator (checked on the BridgeBIM case studies and on the example bridges).

## Permanent loads

They are an addition of the plugin (the original applied no loads). Two load cases:

* **`G1`, self weight**: girders, cross-beams, stringers and bracing weigh `gamma A` (steel
  increased by `maggiorazione_acciaio`, default 1.10, for bolts, studs, splices and erection
  bracing); the slab (`peso_cls`, thickness `spessore_soletta`) and the kerbs (thickness equal to
  the slab) are line loads on the transverse elements, with half-bay tributary width on each side,
  because their strips are fictitious stiffness elements; rigid links weigh nothing;
* **`G2`, superimposed dead load**: pavement (`peso_asfalto`, `spessore_asfalto`, between edges)
  and optional barriers (`barriera`, kN/m per side, nodal forces on the kerbs).

The vertical totals are in the result (`data["totali_kN"]`) and equal the real volume times the
unit weight: the pavement is `22 kN/m³ x thickness x width between edges x length`. The
combinations proposed in `Result.combinations` are `SLE_rara` (1 and 1) and `SLU_permanenti`
(1.35 and 1.5).

## Input sheets (same Excel as BridgeBIM)

| Sheet | Content |
|---|---|
| `Conci` | one row per segment: `Lunghezza` (m), `Hstart`, `Hend` (mm), `sa` (web), `psup`, `pinf` (flanges, e.g. `600x30` or `600x30+400x20`); for concrete also `B`, `nt`, `int`, `gerber` (`SI`/`NO`) with `psup` = girder width |
| `Campate` | span lengths in m (first column): they give the bearing lines |
| `As` | axis and slab edges: `X1..Z5`, `P1` left edge, `P4` right edge, `P5` axis (m) |
| `At` | axes of the edge girders (`P1`, `P4`); if missing, `As` is used |
| `traversi` | `prog` (spacing from the previous cross-beam, m), `Hstart`, `Hend`, `sa`, `psup`, `pinf` and the same with suffix `_rs` / `_rd` for the cantilevers |

With `auto` the type is deduced from the `Conci` sheet: the columns `B`, `nt`, `int` mean
concrete; the column `Fondo` or the sheets `controventi_W` and `traversi_inf` mean box; the rest
is girder type (two, or `n_travi`).

## Actions

| Action | What it does | Main outputs |
|---|---|---|
| `leggi_excel` | reads the BridgeBIM Excel and builds the model | model with `G1` and `G2`, summary |
| `genera` | builds a straight bridge from the main parameters, without Excel | model with `G1` and `G2`, summary |
| `modello_di_esempio` | the example bridge of the type, with 1.5 kN/m barriers | model, summary |
| `controllo` | trial static analysis: equilibrium, reactions, deflections | equilibrium and reaction tables, deflection chart, warnings |

### `genera` parameters

| Parameter | Type | Unit | Default | Notes |
|---|---|---|---|---|
| `tipologia` | choice | | `misto_2travi` | see above |
| `campate` | text | m | `30, 40, 30` | comma-separated spans |
| `larghezza` | number | m | 10 | slab, edge to edge |
| `interasse` | number | m | 6 | edge girders, box webs, concrete girders |
| `altezza` | number | mm | 1800 | steel girder or concrete girder |
| `n_travi` | integer | | 3 | grillage and concrete |
| `passo_traversi` | number | m | 5 | |
| `gerber_lunghezza` | number | m | 0 | suspended span at the centre of the last central span (concrete) |
| `spessore_soletta` | number | mm | 280 | |
| `spessore_asfalto` | number | mm | 80 | |
| `larghezza_cordolo` | number | mm | 1700 | |
| `barriera` | number | kN/m | 0 | per side, in `G2` |
| `passo_max_trasv` | number | m | 1 | |
| `con_longherina` | boolean | | false | two girders and grillage |
| `con_fasi` | boolean | | false | composite: phased composite sections |
| `parametri` | JSON | | | any generator parameter (mm): `offset_sx`, `offset_dx`, `con_controventi`, `fasi_beff`, `fasi_nL`, `peso_asfalto`, `maggiorazione_acciaio`, `classe_cls_travi`, ... |

`leggi_excel` has `file`, `tipologia` (`auto`), `carichi` and `parametri`; `controllo` has `file`
(empty: example bridge), `tipologia` and `parametri`.

## Examples

From the library:

```python
from feagent import plugins
p = plugins.get("bridge")

r = p.run("genera", tipologia="misto_2travi", campate="30, 40, 30",
          larghezza=10, interasse=6, altezza=1800, barriera=1.5)
m = r.model                          # feagent.Model in N and m, cases G1 and G2
res = m.solve(sparse=True, cases={"G1": 1.0, "G2": 1.0})

r = p.run("leggi_excel", file="input_cassone_01.xlsx")     # type deduced
r = p.run("controllo", tipologia="ca_rettangolare")        # equilibrium and deflection
print(r.data["uz_min_mm"])
```

From the command line:

```bash
feagent plugin run bridge modello_di_esempio --param tipologia=misto_cassone
feagent plugin run bridge leggi_excel --param file=input_2travi_01.xlsx
```

## Verification

The tests (`tests/test_plugin_bridge.py`) check:

* the tables against the original BridgeBIM code, run once on the same sheets (nodes, elements,
  sections, supports and property sums, cell by cell);
* equilibrium (sum of reactions equal to the sum of loads) and the `G1` and `G2` totals from the
  geometry;
* the deflection of a 30 m simply supported girder, `5 q L⁴ / 384 E I` with `I` computed from the
  plates, within 1 per cent (the rest is the discretisation of the load on the nodes);
* the Gerber hinges (zero moment at the release, non zero without it);
* reading from Excel and generating from parameters, which give the same model.

One-off cross-check with OpenSees (not a dependency): on the example two-girder and grillage
models the nodal displacements coincide with feagent's to `1e-10` (relative to the maximum
displacement), with the same equivalent nodal forces.

## Limits

* the grillage is planar: torsion and transverse distribution are those of the strips, not a
  plate; the bracing low nodes sit at `-h` with supports on the low nodes, as in the original;
* in concrete the cross-beam weight is added to the slab weight without removing the part of web
  inside the slab thickness (a small difference, on the safe side);
* no moving or variable loads: the plugin provides the model and the permanent loads, moving
  loads can be added with the feagent moving-load module;
* no IFC, CAD, Tekla, Revit or bill of quantities export, which stay in BridgeBIM;
* a bridge generated from parameters is straight and at constant level; curved or sloping bridges
  are described with the Excel (sheets `As` and `At`).
