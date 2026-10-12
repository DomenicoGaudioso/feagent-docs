---
layout: default
title: "60 - Field problems: heat, torsion, potential flow, seepage"
parent: English
nav_order: 60
---

# 60 - Field problems: heat, torsion, potential flow, seepage

The `feagent.field` module solves the **scalar** problems governed by the
quasi-harmonic equation, which in finite element textbooks come right after
stress analysis (Cook, Malkus & Plesha ch. 15-16; Seshu ch. 2-4 and 6; Cook,
*Modeling*, ch. 8):

```
div(k grad phi) + Q = c dphi/dt
```

| `problem` | phi | k | Q | derived quantity |
|---|---|---|---|---|
| `heat` | temperature | conductivity | heat generated per volume | flux `q = -k grad T` |
| `torsion` | Prandtl stress function | 1 | `2 G theta` | `tau_xz = dphi/dy`, `tau_yz = -dphi/dx`, `T = 2 int phi dA` |
| `potential` | velocity potential | 1 | 0 | `v = grad phi` |
| `stream` | stream function | 1 | 0 | `u = dpsi/dy`, `v = -dpsi/dx` |
| `seepage` | piezometric head | permeability | 0 | Darcy velocity `v = -k grad h` |
| `generic` | any | k | Q | `-k grad phi` |

Galerkin discretisation gives `C dphi/dt + K phi = F` with

```
K = int B^T k B dV + int h N^T N dS           (conduction + convection)
C = int rho c N^T N dV                         (capacity)
F = int N^T Q dV + int N^T q dS + int h phi_inf N^T dS
```

## Elements

Field elements use **the same shape functions, quadratures and side/face
numbering** as the structural elements, so a structural mesh can be reused as it is:

| type | geometry | notes |
|---|---|---|
| `L2`, `L3` | 1D bar along any line | cross-section `area`, `perimeter` for the lateral convection of fins; `L3`: ends, then midside node |
| `T3`, `T6`, `Q4`, `Q8`, `Q9` | X-Y plane | thickness `t`, or `axisym=True` (X = radius, Y = axis, full ring) |
| `Tet4`, `Tet10`, `Hex8`, `Wedge6`, `Pyramid5` | 3D solids | faces as in `feagent.solid` |

## Boundary conditions and loads

| method | meaning |
|---|---|
| `set_value(node, value)` | prescribed value (also a function of time in transients) |
| `fix_boundary(value, exclude)` | prescribes the value on the whole free boundary of a plane mesh (torsion) |
| `add_source(elem, Q)` | generation per unit volume (constant or nodal values) |
| `add_point_source(node, Q)` | concentrated source |
| `add_flux(elem, side, q)` | prescribed inward flux |
| `add_convection(elem, side, h, phi_inf)` | convection on a side or face |
| `add_lateral_convection(elem, h, phi_inf, faces)` | convection on the lateral surface: 1D fins (perimeter) or plate faces |
| `add_nodal_convection(node, hA, phi_inf)` | concentrated convection (fin tip) |
| `add_radiation(elem, side, eps, phi_inf)` | radiation, nonlinear, solved by Newton; absolute temperatures `phi + temperature_offset` |

## Analyses

* `solve()`: steady state (Newton when there is radiation) -> `FieldResult`;
* `solve_transient(dt, t_end, initial, theta=0.5, lumped=False)`: theta method
  (Crank-Nicolson, backward Euler with `theta = 1`, Galerkin with `2/3`,
  explicit with `0`) -> `TransientFieldResult` (`value_history`, `at(t)`, `final()`);
* `eigenvalues(n)`: eigenvalues `K x = lambda C x` (time constants `1/lambda`;
  with `rho c = 1/c^2` they are the acoustic modes `omega^2` of a fluid).

Results (`FieldResult`): `value`, `values`, `value_at`, `gradient`, `flux`,
`velocity`, `nodal_gradients`, `nodal_flows` (the "reaction": flow injected into
the body by the prescribed nodes), `convection_flow`, `radiation_flow`; for torsion
`torque`, `torsion_constant`, `shear_stress`, `max_shear_stress`.

## Coupling with thermal stresses

`FieldResult.to_model_temperatures(model, reference)` applies the temperature field
as a temperature change `T - reference` (nodal values) to the plane and solid
elements of a `feagent.Model` built with the same node and element ids: the
computed thermal field becomes the load of the stress analysis
(`Model.add_solid_thermal`).

```python
from feagent import Material, Model
from feagent.field import FieldModel, rect_mesh

nodes, els, _ = rect_mesh(0.4, 0.1, 20, 5, "Q8")
fm, m = FieldModel("heat"), Model()
for k, (x, y) in nodes.items():
    fm.add_node(k, x, y)
    m.add_node(k, x, y, 0.0)
for e, nn in els:
    fm.add_element(e, nn, 45.0, t=0.02)
    m.add_plane(e, nn, Material(210e9, 0.3, 1.2e-5), "stress", t=0.02)
for k, (x, y) in nodes.items():
    if x == 0.0:
        fm.set_value(k, 200.0)
for e in range(1, 21):                       # convective upper side
    fm.add_convection(e + 80, 2, 25.0, 20.0)
field = fm.solve()
field.to_model_temperatures(m, reference=20.0)
```

## Verification examples

![Torsion](images/books/lib_torsione_rettangolo.png)

*Saint-Venant torsion of a 2:1 rectangle (one quarter with symmetry, Q8): Prandtl
function and shear stresses; torsion constant within 0.2 % of the exact value
0.229 a b³.*

![Fin](images/books/lib_aletta_transitorio.png)

*Aluminium fin (Seshu, Ex. 4.10 and 6.7): temperature profiles in the transient
and at steady state.*

The tests `tests/test_field.py` compare the module with closed-form solutions:
fin with adiabatic tip (cosh), wall with generation (parabola), plate with a
sinusoidal boundary temperature (sinh), torsion of circle, square and rectangle,
axisymmetric hollow cylinder (logarithm), transient of a slab (Fourier series),
radiating wall, uniform potential, stream and seepage fields. The textbook worked
examples are in pages 62-65.

References: Cook, Malkus, Plesha & Witt, *Concepts and Applications of FEA*;
Seshu, *Textbook of Finite Element Analysis*; Lewis, Nithiarasu & Seetharamu,
*Fundamentals of the FEM for Heat and Fluid Flow*; Timoshenko & Goodier, *Theory
of Elasticity* (torsion).
