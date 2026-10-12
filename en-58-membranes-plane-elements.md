---
layout: default
title: "58 - Membranes, shear panels and plane elements"
parent: English
nav_order: 58
---

# 58 - Membranes, shear panels and plane elements

Besides the full shell (membrane plus Mindlin bending), feagent has the plate
types of commercial codes that work **only in their own plane**:

| Type | How to create it | Stiffness | Typical use |
|---|---|---|---|
| 3D membrane | `add_shell(..., formulation="membrane")`, `add_shell_tri(..., formulation="membrane")` | full plane stress, in any plane of space | fabrics, tension sheets, webs and flanges modelled without bending |
| Shear panel | `add_shell(..., formulation="shear_panel")` (4 nodes only) | shear flow only, $N_{xy} = G\,t\,\gamma_{xy}$ | thin webs and diaphragms stiffened by beam stringers and posts |

In the Excel sheet `Shell` use the `Formulation` column (`membrane`,
`shear_panel`; triangles also keep `cst` and `thin`).

## 3D membrane

Same geometry, same local frame and same membrane as the Q4 shell (2x2 Gauss)
and the T3 shell (constant strain), without bending and without drilling
stiffness:

$$
\mathbf{N} = \mathbf{C}\,(\boldsymbol{\varepsilon} - \boldsymbol{\varepsilon}_0),
\qquad
\mathbf{C} = \frac{E\,t}{1-\nu^2}
\begin{bmatrix} 1 & \nu & 0 \\ \nu & 1 & 0 \\ 0 & 0 & \tfrac{1-\nu}{2} \end{bmatrix}
$$

The thermal load acts with the uniform change $\Delta T$ only (the through
thickness gradient has no effect). The results (`Result.shell_forces`) contain
$N_x$, $N_y$, $N_{xy}$; moments and shear forces are zero.

## Shear panel

The panel carries the shear flow only, with constant shear strain (one
integration point): the classical model of thin stiffened panels (Kuhn,
*Stresses in aircraft and shell structures*, 1956; Przemieniecki, *Theory of
matrix structural analysis*, 1968, ch. 5):

$$
N_{xy} = G\,t\,\gamma_{xy}, \qquad N_x = N_y = 0
$$

The shear refers to the edge axes (x along side 1-2), which is why the panel
exists with 4 nodes only: in a triangle the local axis would lie along the
diagonal and the shear would have no physical meaning. The panel has no
stiffness for normal strains and its thermal load is zero: it must always be
stiffened by beams or truss bars along its edges, otherwise the model is a
mechanism.

Analytical check: square panel with rigid truss edges, fixed at the base and
loaded at the top by $V$: the displacement is $\delta = V/(G\,t)$ and
$N_{xy} = V/a$ (test `tests/test_membrane_shear_panel.py`).

## Degrees of freedom

Membranes and panels use the node translations only. The rotations of nodes
connected only to these elements (or to solids) have no stiffness and are
restrained automatically by the solver; a nodal moment on such a node raises
an error, because it would be lost. Out of its plane a flat membrane has no
stiffness: restrain it, stiffen it with beams or use it in a closed mesh.

```python
from feagent import Material, Model
from feagent.shell import ShellSection

m = Model()
...
m.add_shell(1, [1, 2, 3, 4], Material(210e9, 0.3, 1.2e-5), ShellSection(t=0.012),
            formulation="shear_panel")       # cross-girder web between beam stringers
```

## 2D plane elements

`add_plane(id, nodes, material, formulation, t, kind, incompatible)` creates
an element in the global **X-Y** plane (all nodes at the same Z). Excel sheet
`Plane`: `Plane`, `Nodes`, `Material`, `Type`, `Formulation`, `t`,
`Incompatible`.

| Type | Nodes | Integration | Notes |
|---|---|---|---|
| T3 | 3 | 1 point | constant strain |
| T6 | 6 (corners, then mid-side nodes 1-2, 2-3, 3-1) | 3 points | quadratic |
| Q4 | 4 | 2x2 Gauss | with `incompatible=True` Wilson-Taylor incompatible modes (QM6): no shear locking in bending |
| Q8 | 8 (corners, then mid-side nodes 1-2, 2-3, 3-4, 4-1) | 3x3 Gauss | serendipity, curved sides allowed |

| `formulation` | Assumption | Use | $\sigma_{zz}$ |
|---|---|---|---|
| `stress` | plane stress, thickness `t` | thin plates loaded in their plane, webs, walls | 0 |
| `strain` | plane strain ($\varepsilon_{zz} = 0$), thickness `t` (usually 1) | sections of long structures: walls, tunnels, dams, embankments | $\nu(\sigma_{xx} + \sigma_{yy}) - E\alpha\Delta T$ |
| `axisym` | axisymmetric: X = radius $r \ge 0$, Y = axis | tanks, silos, piles, circular plates and footings | hoop stress $\sigma_\theta$ |

In axisymmetry the hoop strain is $\varepsilon_\theta = u_r/r$ and volumes,
masses and loads refer to the **full ring** ($2\pi r$): a nodal force is the
resultant over the whole circumference.

Plane elements live with the solids (shared ids): body forces
(`add_solid_body_force`, the Z component is ignored), side loads
(`add_solid_pressure` and `add_solid_surface_load` with `face` = side, side k
goes from corner node k+1 to the next one), thermal loads
(`add_solid_thermal`), masses and stresses (`Result.solid_stresses`,
`Result.solid_nodal_stresses`, vector
$[\sigma_{xx}, \sigma_{yy}, \sigma_{zz}, \tau_{xy}, 0, 0]$ with von Mises and the
principal values of the full tensor) are the same. `uz` and the rotations of
nodes connected only to plane elements are restrained automatically; an Fz
force on such nodes raises an error.

In the GUI the commands are under **Model > Plates**. Tool **Q** (Draw plane
elements) works like the shell tool: click 3 or 4 corners in the X-Y plane.
With the quadratic order the mid-side nodes are created automatically and
shared with neighbouring elements, giving T6 and Q8. There are also the plane
mesh generator, the element from the selected nodes and the pressure on the
sides of the selected nodes. A model made only of plane elements opens with
Y up, and the conversion to Z up (which would move the elements out of their
plane) is not offered.

Analytical checks (`tests/test_plane.py`):

* MacNeal-Harder patch test on a distorted mesh, for all types and for plane
  stress and plane strain: exact constant stress;
* plane stress cantilever (L/H = 10): Timoshenko deflection within 2 % with Q8
  and incompatible Q4, while the standard Q4 stays too stiff;
* thick cylinder under internal pressure (Lame'), in plane strain (quarter
  ring in Q8) and axisymmetry: radial displacement within 0.2 %, hoop stress at
  the inner radius within 2 %;
* free thermal expansion (zero stresses) and restrained out of plane
  ($\sigma_{zz} = -E\alpha\Delta T$);
* masses $\rho\,t\,A$ and $2\pi\,r_G\,A\,\rho$; first mode of a cantilever within
  3 % of the Euler beam.

```python
from feagent import Material, Model

m = Model()
...
m.add_plane(1, [1, 2, 3, 4, 5, 6, 7, 8], Material(30e9, 0.2, 1e-5), formulation="axisym")
m.add_solid_pressure(1, 3, 2.0e5)          # pressure on side 4-1
res = m.solve()
s = res.solid_stresses(1)                   # s["szz"] = hoop stress
```
