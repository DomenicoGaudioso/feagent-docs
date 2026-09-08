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
from feagent import postprocess

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

## Combined beam stresses

`Result.beam_stresses` computes normal stresses with Navier's formula
`σ = N/A + My·z/Iy − Mz·y/Iz` at the section recovery points (sign
convention consistent with `internal_forces`: `Mz > 0` puts fibres at
`y < 0` in tension, `My > 0` those at `z > 0`):

```python
sec = Section.rectangular(0.1, 0.3)   # corner points + automatic Wy/Wz
# other helpers: Section.box, Section.tube, Section.double_t
st = res.beam_stresses(1, n=21)
st["sigma"]       # (n, n_points) stresses at the labelled points
st["sigma_max"]   # per-abscissa envelope (also from Wy/Wz moduli alone)
st = res.beam_stresses(1, tau=True)   # + mean shear V/As and T/Wt,
st["svm_max"]                          #   indicative von Mises
```

Points can be defined manually (`section.stress_points = [(y, z, label),
...]` or `points=` at call time); with only the `Wy`/`Wz` moduli you get the
envelope `N/A ± |My|/Wy ± |Mz|/Wz`. Tapered sections are supported
(properties evaluated at each abscissa).

## Shell stresses: principals and nodal map

Shell surface stresses (`res.shell_stresses(id)`) include the **principal
stresses** per surface: `s1_top`/`s2_top`/`angle_top` (and `_bot`), with
`angle` the inclination of principal axis 1 relative to local x (Mohr).

For a **continuous** stress map use the patch-averaged nodal recovery, in
global coordinates:

```python
ns = res.shell_nodal_stresses(side="top")   # or "bot"
ns[node]["sigma"]   # averaged 3x3 global tensor at the node
ns[node]["s1"], ns[node]["s2"], ns[node]["s3"]   # principals (descending)
ns[node]["svm"]     # von Mises
```

Each element contributes its stress (area-weighted) to the nodes it shares;
the nodal principals are the eigenvalues of the averaged tensor.
