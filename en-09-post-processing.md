---
layout: default
title: "09 - Post-Processing"
parent: English
nav_order: 9
---

# 09 - Post-Processing

After solving (`res = m.solve()`), you can compute internal forces and deformed shape along elements.

## Nodal results

```python
res.displacements(node)           # array [ux, uy, uz, rx, ry, rz]
res.displacement(node, "uy")      # single DOF (float)
res.reactions(node)               # array [Fx, Fy, Fz, Mx, My, Mz]
```

## End forces

```python
res.element_forces[elem_id]       # 12×1 vector in local coordinates
# [fx_i, fy_i, fz_i, mx_i, my_i, mz_i, fx_j, fy_j, fz_j, mx_j, my_j, mz_j]
```

## Internal forces along the element

```python
from beamfeapy import postprocess

di = postprocess.internal_forces(res, elem_id, n=101)
# Returns dict: x, N, Vy, Vz, T, My, Mz
```

Components:
- `N`: axial force (positive in tension)
- `Vy`, `Vz`: shear in local y and z directions
- `T`: torsional moment
- `My`: bending moment about y (x-z plane)
- `Mz`: bending moment about z (x-y plane)

**European convention**: negative moment drawn at the extrados.

## Local displacements along the element

```python
dd = postprocess.element_displacements(res, elem_id, n=51)
# Returns dict: x, u_local (n×6 array) = [ux, uy, uz, rx, ry, rz]
```

## Global deformed shape

```python
pts = postprocess.deformed_shape_global(res, elem_id, n=51, scale=100)
# Returns n×3 array of global coordinates (deformed, scaled)
```

## Complete example

```python
res = m.solve(cases=["G", "Q"])

# Displacements
print(f"Tip deflection: {res.displacement(2, 'uy'):.4e} m")

# Reactions
print(f"Left support: {res.reactions(1)[:3]}")

# Mz diagram along element 2
di = postprocess.internal_forces(res, 2, n=101)
print(f"Mz max = {max(abs(di['Mz'])):.1f} Nm")
print(f"Mz at mid = {di['Mz'][50]:.1f} Nm")

# Axial force
print(f"N range: [{di['N'].min():.0f}, {di['N'].max():.0f}] N")
```
