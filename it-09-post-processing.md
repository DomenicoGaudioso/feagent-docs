---
layout: default
title: "09 - Post-Processing"
parent: Italiano
nav_order: 9
---

# 09 - Post-Processing

Dopo la soluzione (`res = m.solve()`), si possono calcolare le azioni interne e la deformata lungo gli elementi.

## Risultati nodali

```python
res.displacements(node)           # array [ux, uy, uz, rx, ry, rz]
res.displacement(node, "uy")      # singolo GdL (float)
res.reactions(node)               # array [Fx, Fy, Fz, Mx, My, Mz]
```

## Forze d'estremità

```python
res.element_forces[elem_id]       # vettore 12×1 in coordinate locali
# [fx_i, fy_i, fz_i, mx_i, my_i, mz_i, fx_j, fy_j, fz_j, mx_j, my_j, mz_j]
```

## Azioni interne lungo l'elemento

```python
from beamfeapy import postprocess

di = postprocess.internal_forces(res, elem_id, n=101)
# Returns dict: x, N, Vy, Vz, T, My, Mz
```

Componenti:
- `N`: sforzo normale (positivo in trazione)
- `Vy`, `Vz`: taglio nelle direzioni locali y e z
- `T`: momento torcente
- `My`: momento flettente attorno a y (piano x-z)
- `Mz`: momento flettente attorno a z (piano x-y)

**Convenzione europea**: momento negativo disegnato all'estradosso.

## Spostamenti locali lungo l'elemento

```python
dd = postprocess.element_displacements(res, elem_id, n=51)
# Returns dict: x, u_local (n×6 array) = [ux, uy, uz, rx, ry, rz]
```

## Deformata globale

```python
pts = postprocess.deformed_shape_global(res, elem_id, n=51, scale=100)
# Returns n×3 array of global coordinates (deformed, scaled)
```

## Esempio completo

```python
res = m.solve(cases=["G", "Q"])

# Spostamenti
print(f"Tip deflection: {res.displacement(2, 'uy'):.4e} m")

# Reazioni
print(f"Left support: {res.reactions(1)[:3]}")

# Diagramma Mz lungo elemento 2
di = postprocess.internal_forces(res, 2, n=101)
print(f"Mz max = {max(abs(di['Mz'])):.1f} Nm")
print(f"Mz at mid = {di['Mz'][50]:.1f} Nm")

# Forza normale
print(f"N range: [{di['N'].min():.0f}, {di['N'].max():.0f}] N")
```