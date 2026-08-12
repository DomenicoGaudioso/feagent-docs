---
layout: default
title: "17 - API Reference"
parent: English
nav_order: 17
---

# 17 - API Reference

Complete reference of all public functions in **beamfeapy**. For illustrated
examples of each family see [Illustrated Functions](en-18-illustrated-functions.html).

Typical import:

```python
from beamfeapy import Model, Material, Section, VariableSection, read_excel
from beamfeapy import postprocess
from beamfeapy.plotting import (plot_model, plot_loads, plot_diagram,
                                plot_deformed, plot_reactions, plot_internal_forces)
```

---

## Materials and Sections

### `Material(E, nu=0.3, alpha=0.0, G=None, rho=0.0, name="")`
Isotropic elastic material. `G` (shear modulus) is derived as
`E/(2(1+nu))` if not provided. `alpha` = thermal expansion; `rho` = density
(not used in static analysis).

### `Section(A, Iy, Iz, J, Asy=None, Asz=None, h_y=None, h_z=None, name="")`
Section properties (principal local axes y, z).
- `A` area; `Iy` inertia about y (x-z plane); `Iz` about z (x-y plane); `J` torsion.
- `Asy`, `Asz` effective shear areas — **Timoshenko only** (`shear=True`).
- `h_y`, `h_z` section heights — **for thermal gradient loads only**.

### `VariableSection(A, Iy, Iz, J, Asy=None, Asz=None, breakpoints=())`
Variable section along the element: each property is a constant value or a
function of `xi = x/L ∈ [0,1]`.
- **`VariableSection.from_sections(sec_i, sec_j)`** — linear interpolation between two sections.
- **`VariableSection.from_stations([(xi, sec), ...])`** — piecewise linear between stations (xi=0 and 1 required); internal abscissae become `breakpoints`.
- **`VariableSection.rectangular(b, h, E_shear=False)`** — rectangular section with `b(xi)`, `h(xi)` (constants or callables).

---

## Model

### `Model()`
FEM model container. Attributes: `nodes`, `elements`, `sections`,
`nodal_loads`, `concentrated_loads`, `distributed_loads`, `thermal_loads`,
`prestress_loads`, `settlements`.

### `add_node(id, x, y, z) -> Node`
Add a node (6 DOFs: `ux, uy, uz, rx, ry, rz`).

### `add_section(id, section=None, **props) -> Section`
Register a section with a reusable `id`. Pass a `Section` object or parameters
(`A=`, `Iy=`, ...). Useful for defining elements by section ID.

### `add_beam(id, node_i, node_j, material, section, ref_vector=None, roll=0.0, shear=False, releases_i=None, releases_j=None) -> BeamElement3D`
Prismatic 3D beam element (Euler-Bernoulli; Timoshenko if `shear=True`).
- `section`: a `Section` object **or id** registered with `add_section`.
- `ref_vector`: vector that, with local x-axis, defines the x-y plane (section orientation). If `None`, automatic selection (global Z, Y for vertical elements).
- `roll`: angle [rad] of section rotation about local x-axis.
- `shear`: if `True` uses Timoshenko (requires `Asy`/`Asz` in section).
- `releases_i`, `releases_j`: lists of released DOFs at ends, e.g. `["rz"]` (flexural hinge), `["ry","rz"]`. Removed by static condensation.

### `add_tapered_beam(id, node_i, node_j, material, varsection=None, section_i=None, section_j=None, stations=None, ref_vector=None, roll=0.0, shear=False, n_gauss=8)`
**Variable-section** element (exact force-based stiffness). Section is specified
in three ways:
- `varsection`: a `VariableSection`;
- `section_i`, `section_j`: section (or id) at start/end → linear interpolation;
- `stations={xi: section, ...}` or `[(xi, section), ...]` → piecewise linear.

### Constraints
- **`fix(node, dofs=None)`** — restrain listed DOFs (`["ux","uy",...]`); `None` = all 6 (fixed).
- **`pin(node)`** — spherical hinge: restrains 3 translations.
- **`support(node, ux=False, uy=False, ..., rz=False)`** — selective constraint, e.g. `support(1, uy=True, uz=True)`.

### `add_settlement(node, dof, value) -> Settlement`
Settlement (imposed displacement/rotation): `dof` ∈ `{ux,uy,uz,rx,ry,rz}`.

---

## Loads

All `add_*` methods accept `case="..."` (load case, default `"default"`).

### `add_nodal_load(node, case="default", Fx=0, Fy=0, Fz=0, Mx=0, My=0, Mz=0) -> NodalLoad`
Concentrated force/moment at a node (global system).

### `add_concentrated_load(elem, xi, Fx=0, Fy=0, Fz=0, Mx=0, My=0, Mz=0, frame="local", case="default")`
Concentrated force/moment at an **internal abscissa** `xi ∈ [0,1]` of the element.
`frame`: `"local"` (default) or `"global"`. Equivalent load `N(ξL)ᵀ[F,M]`.

### `add_distributed_load(elem, component, q_i, q_j=None, a=None, b=None, frame="local", case="default")`
Distributed load on an element. `component` ∈ `{fx,fy,fz,mx,my,mz}` (forces and
**distributed moments**). `q_i`→`q_j` linear (trapezoidal; uniform if `q_j=None`);
`a,b` for a **partial** span. `frame`: `"local"`/`"global"`.

### `add_thermal_load(elem, dT_axial=0, dT_grad_y=0, dT_grad_z=0, case="default")`
Uniform temperature change (`dT_axial`) and linear gradient along y/z (requires
`h_y`/`h_z` in section).

### `add_thermal_profile(elem, profile, axis="y", width=None, n_section=24, case="default")`
**Generic** thermal profile along the height: `profile` is a function `T(s)` or
a sequence of points `[(s, T), ...]`. Projection onto uniform+linear part +
self-equilibrating residual (eigenstress, EN 1991-1-5).

### `add_prestress(elem, P, e_i=0, e_j=None, plane="y", sag=0.0, profile=None, case="default")`
Prestress from cable (equivalent load method): tension `P`, eccentricity
`e_i`→`e_j` + parabolic sag `sag` (or `profile(xi)`). `plane` ∈ `{y,z}`.

### `add_cable_prestress(P, points, elements=None, case="default")`
Prestress from **3D cable geometry**: `points` = polyline of global
coordinates, `P` = tension. Computes anchor forces (`±P·t`) and deviation
(`P·(t_out−t_in)`) and applies them as eccentric concentrated loads on beams.

---

## Solution

### `load_cases() -> list[str]`
Sorted list of load cases present in the loads.

### `solve(sparse=False, cases=None) -> Result`
Solve the system.
- `sparse`: `True` uses COO→CSR triplet assembly and sparse SuperLU solver (large models).
- `cases`: load combination —
  - `None` = all loads (coeff 1);
  - string = a single load case;
  - list/set = combination (coeff 1 each);
  - **dict `{case: coefficient}`** = combination with **multiplicative coefficients** for each load pattern, e.g. ULS `solve(cases={"G": 1.35, "Q": 1.5})`.

### `assemble_mass(mass_source, g=9.81) -> ndarray`
Concentrated mass vector (diagonal, ndof) obtained from the loads of the
specified load cases. `mass_source` is a dict `{case: coefficient}`: mass = coeff ·
|force| / g, assigned to the 3 translational DOFs of each node. Distributed loads
are distributed to nodes (equivalent nodal forces), concentrated span loads
are linearly interpolated. Include gravitational (vertical) load cases in the source.

### `modal(n_modes=10, mass_source=None, g=9.81) -> ModalResult`
Modal analysis: solves `K φ = ω² M φ` on free DOFs, with masses from `mass_source`
(the user chooses which load cases to convert to mass and with what coefficient).
Free DOFs **without mass** (rotational and unloaded translational) are eliminated
by **static condensation** (no spurious modes). Validated vs OpenSees to machine
precision.

#### `ModalResult`
Attributes: `omega` [rad/s], `freq` [Hz], `period` [s], `phi` (ndof × n_modes,
mass-normalized shapes), `eff_mass` (n_modes × 3), `part`, `total_mass`.
Methods: `mode(i)`, `mode_shape(i, node)`, `mass_participation()` (ratios per
mode and direction X/Y/Z), `summary()`.

### `from_excel(path)` *(classmethod)* / `assemble_stiffness()` / `assemble_stiffness_sparse()` / `assemble_loads(cases=None)`
`Model.from_excel(path)` builds the model from Excel (see [Excel I/O](en-11-excel-io.html)).
The `assemble_*` methods expose the stiffness matrix and load vector.

---

## Results (`Result`)

Attributes: `U` (global displacements), `R` (global reactions),
`element_forces` (local end forces per element, 12),
`element_local_disp`, `cases`.

- **`displacements(node) -> ndarray(6)`** — `[ux,uy,uz,rx,ry,rz]` of the node.
- **`displacement(node, dof) -> float`** — single component.
- **`reactions(node) -> ndarray(6)`** — `[Fx,Fy,Fz,Mx,My,Mz]` of the node.
- **`to_excel(path, n_diagram=0)`** — export displacements, reactions, end forces (and diagrams if `n_diagram>1`).

---

## Post-processing (`beamfeapy.postprocess`)

### `internal_forces(result, elem_id, n=51) -> dict`
Internal forces along the element. Returns `{x, N, Vy, Vz, T, My, Mz}` (arrays
of `n` values). N>0 tension; includes contributions from distributed and
concentrated loads (jumps).

### `element_displacements(result, elem_id, n=21) -> dict`
`{x, u_local}` (n×6): local displacements interpolated along the element.

### `deformed_shape_global(result, elem_id, n=21, scale=1.0) -> ndarray(n,3)`
Global coordinates of the deformed axis (amplified by `scale`).

---

## Visualization (`beamfeapy.plotting`)

Requires the `plot` extra (`plotly`, `kaleido`). Each function returns a
`plotly.graph_objects.Figure` (`.show()`, `.write_html(...)`, `.write_image(...)`).

- **`plot_model(model, show_node_ids=True)`** — geometry (nodes, elements).
- **`plot_loads(model, case=None, scale=0.14)`** — structure + loads (filterable by load case).
- **`plot_diagram(result, component="Mz", scale=None, n=41)`** — internal force diagram along the structure, drawn as **bicolor filled area** (positive and negative with different colors and transparency; bending: positive light blue, negative light red). Each action is plotted on its **usage axis** (N shares the axis of My). Moments in European convention (negative at extrados). Labels on global max and min.
- **`plot_deformed(result, scale=1.0, n=21)`** — deformed configuration.
- **`plot_reactions(result, scale=0.14)`** — reactions (forces and moments) at supports.
- **`plot_internal_forces(result, elem_id, n=101)`** — the 6 2D diagrams for one element.
- **`plot_mode(modal_result, i=0, scale=1.0, n=21)`** — draws the i-th mode shape (with frequency and period in the title).

---

## Excel Import/Export (`beamfeapy.io_excel`)

- **`read_model(path)`** (alias `beamfeapy.read_excel`, `Model.from_excel`) — builds the model from Excel sheets.
- **`write_results(result, path, n_diagram=0)`** (alias `Result.to_excel`) — exports results.
- **`write_template(path)`** — generates a fillable template workbook.

Sheets: `Node, Material, Section, Element, Support, NodalLoad, DistributedLoad,
ConcentratedLoad, Thermal, Settlement, Prestress` (see [Excel I/O](en-11-excel-io.html)).
