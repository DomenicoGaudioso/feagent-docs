---
layout: default
title: "18 - Illustrated Functions"
parent: English
nav_order: 18
---

# 18 - Illustrated Functions

Every function family in the library is explained here with a complete example,
illustrated with the five standard views: **model**, **applied loads**,
**deformed shape**, **internal forces** (Mz diagram, European convention) and
**support reactions**. For complete signatures see
[API Reference](en-17-api-reference.html). Images are regenerated with
`python scripts/_gen_function_docs.py`.

> Force diagrams are **bicolor filled areas by sign**
> (positive / negative with transparency): positive bending is light blue,
> negative is light red. Each action is on its usage axis (N on My's axis).

---

## 1. Nodal loads — `add_nodal_load`

Concentrated force and/or moment at a node (global system). Example: cantilever
with vertical force and moment at the free end.

```python
m = Model(); m.add_node(1, 0, 0, 0); m.add_node(2, 4, 0, 0)
m.add_beam(1, 1, 2, Material(210e9, 0.3), Section(1e-2, 2e-5, 3e-5, 1e-5))
m.fix(1)
m.add_nodal_load(2, Fy=-10000.0, Mz=8000.0)
res = m.solve()
```

| Model | Loads | Deformed |
|---|---|---|
| ![](images/fn_nodal_model.png) | ![](images/fn_nodal_loads.png) | ![](images/fn_nodal_deformed.png) |

| Internal forces (Mz) | Reactions |
|---|---|
| ![](images/fn_nodal_forces.png) | ![](images/fn_nodal_reactions.png) |

---

## 2. Concentrated loads in span — `add_concentrated_load`

Force/moment at an internal abscissa `xi ∈ [0,1]`. The diagram shows the **jump**
at the load location.

```python
m.add_concentrated_load(1, 0.35, Fy=-2e4)     # force at 0.35 L
m.add_concentrated_load(1, 0.70, Mz=1.5e4)    # moment at 0.70 L
```

| Model | Loads | Deformed |
|---|---|---|
| ![](images/fn_concentrated_model.png) | ![](images/fn_concentrated_loads.png) | ![](images/fn_concentrated_deformed.png) |

| Internal forces (Mz) | Reactions |
|---|---|
| ![](images/fn_concentrated_forces.png) | ![](images/fn_concentrated_reactions.png) |

---

## 3. Distributed loads — `add_distributed_load`

Uniform, partial (`a,b`), trapezoidal (`q_i→q_j`); forces and moments
(`fx,fy,fz,mx,my,mz`), in local or global coordinates.

```python
m.add_distributed_load(e, "fy", -3000.0)                 # uniform
m.add_distributed_load(e, "fy", -4000.0)                  # heavier segment
```

| Model | Loads | Deformed |
|---|---|---|
| ![](images/fn_distributed_model.png) | ![](images/fn_distributed_loads.png) | ![](images/fn_distributed_deformed.png) |

| Internal forces (Mz) | Reactions |
|---|---|
| ![](images/fn_distributed_forces.png) | ![](images/fn_distributed_reactions.png) |

---

## 4. Thermal loads — `add_thermal_load` / `add_thermal_profile`

Uniform change + linear gradient (or generic profile). Example: portal frame
with `dT_axial` and gradient on the beam.

```python
m.add_thermal_load(2, dT_axial=25.0, dT_grad_y=20.0)
```

| Model | Loads (structure+supports) | Deformed |
|---|---|---|
| ![](images/fn_thermal_model.png) | ![](images/fn_thermal_loads.png) | ![](images/fn_thermal_deformed.png) |

| Internal forces (Mz) | Reactions |
|---|---|
| ![](images/fn_thermal_forces.png) | ![](images/fn_thermal_reactions.png) |

> Thermal loads are not arrows: the "loads" view shows the structure and
> supports; the effect is seen in the deformed shape and internal forces.

---

## 5. Nodal settlements — `add_settlement`

Imposed displacement/rotation at a support. Example: support settling by 10 mm.

```python
m.add_settlement(node_dx, "uy", -0.01)
```

| Model | Loads | Deformed |
|---|---|---|
| ![](images/fn_settlement_model.png) | ![](images/fn_settlement_loads.png) | ![](images/fn_settlement_deformed.png) |

| Internal forces (Mz) | Reactions |
|---|---|
| ![](images/fn_settlement_forces.png) | ![](images/fn_settlement_reactions.png) |

---

## 6. Prestress — `add_prestress` / `add_cable_prestress`

Cable by eccentricity or from 3D geometry. Example: parabolic cable (P=2 MN,
sag=0.35 m): primary moment `P·sag`, camber.

```python
m.add_prestress(1, P=2.0e6, sag=0.35)
```

| Model | Equivalent loads | Deformed (camber) |
|---|---|---|
| ![](images/fn_prestress_model.png) | ![](images/fn_prestress_loads.png) | ![](images/fn_prestress_deformed.png) |

| Internal forces (Mz) | Reactions |
|---|---|
| ![](images/fn_prestress_forces.png) | ![](images/fn_prestress_reactions.png) |

---

## 7. End releases (hinges) — `releases_i` / `releases_j`

Release a DOF at the element end (static condensation). Example: beam with
internal flexural hinge under distributed load.

```python
m.add_beam(1, 1, 2, mat, sec, releases_j=["rz"])   # hinge (Mz=0) at end j
```

| Model | Loads | Deformed |
|---|---|---|
| ![](images/fn_releases_model.png) | ![](images/fn_releases_loads.png) | ![](images/fn_releases_deformed.png) |

| Internal forces (Mz) | Reactions |
|---|---|
| ![](images/fn_releases_forces.png) | ![](images/fn_releases_reactions.png) |

---

## 8. Tapered section — `add_tapered_beam` / `VariableSection`

Non-prismatic member with a single element (exact stiffness). Example:
tapered cantilever 0.70 → 0.30 m with distributed and tip loads.

```python
vs = VariableSection.rectangular(0.30, lambda xi: 0.70 - 0.40 * xi)
m.add_tapered_beam(1, 1, 2, mat, vs)
```

| Model | Loads | Deformed |
|---|---|---|
| ![](images/fn_tapered_model.png) | ![](images/fn_tapered_loads.png) | ![](images/fn_tapered_deformed.png) |

| Internal forces (Mz) | Reactions |
|---|---|
| ![](images/fn_tapered_forces.png) | ![](images/fn_tapered_reactions.png) |

---

## 9. Timoshenko — `add_beam(..., shear=True)`

Stocky beam with shear deformability (`Asy`/`Asz` areas).

```python
sec = Section(A=A, Iy=..., Iz=..., J=..., Asy=5/6*A, Asz=5/6*A)
m.add_beam(1, 1, 2, mat, sec, shear=True)
```

| Model | Loads | Deformed |
|---|---|---|
| ![](images/fn_timoshenko_model.png) | ![](images/fn_timoshenko_loads.png) | ![](images/fn_timoshenko_deformed.png) |

| Internal forces (Mz) | Reactions |
|---|---|
| ![](images/fn_timoshenko_forces.png) | ![](images/fn_timoshenko_reactions.png) |

---

## 10. 3D Frame — orientation and general analysis

3D frame with elements oriented along different axes (`ref_vector`), distributed
and nodal loads. Shows complete 3D analysis.

```python
m.add_beam(1, 1, 2, mat, sec, ref_vector=(1, 0, 0))   # vertical column
m.add_beam(2, 2, 3, mat, sec, ref_vector=(0, 0, 1))   # beam
```

| Model | Loads | Deformed |
|---|---|---|
| ![](images/fn_frame3d_model.png) | ![](images/fn_frame3d_loads.png) | ![](images/fn_frame3d_deformed.png) |

| Internal forces (Mz) | Reactions |
|---|---|
| ![](images/fn_frame3d_forces.png) | ![](images/fn_frame3d_reactions.png) |
