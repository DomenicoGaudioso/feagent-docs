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
from feagent import postprocess

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
## Tensioni combinate nelle travi

`Result.beam_stresses` calcola le tensioni normali con la formula di Navier
`σ = N/A + My·z/Iy − Mz·y/Iz` nei punti di recupero della sezione
(convenzione coerente con `internal_forces`: `Mz > 0` tende le fibre a
`y < 0`, `My > 0` quelle a `z > 0`):

```python
sec = Section.rectangular(0.1, 0.3)   # punti ai 4 vertici + Wy/Wz automatici
# altri costruttori: Section.box, Section.tube, Section.double_t
st = res.beam_stresses(1, n=21)
st["sigma"]       # (n, n_punti) tensioni nei punti etichettati st["labels"]
st["sigma_max"]   # inviluppo per ascissa (anche da soli moduli Wy/Wz)
st = res.beam_stresses(1, tau=True)   # + taglio medio V/As e T/Wt,
st["svm_max"]                          #   von Mises indicativo
```

I punti si possono definire a mano (`section.stress_points = [(y, z, label),
...]` o `points=` alla chiamata); con i soli moduli `Wy`/`Wz` si ottiene
l'inviluppo `N/A ± |My|/Wy ± |Mz|/Wz`. Supportate anche le sezioni variabili
(proprietà valutate a ogni ascissa).

## Tensioni dei gusci: principali e mappa nodale

Le tensioni superficiali dei gusci (`res.shell_stresses(id)`) includono le
**tensioni principali** per superficie: `s1_top`/`s2_top`/`angle_top` (e `_bot`),
con `angle` l'inclinazione dell'asse principale 1 rispetto a x locale (Mohr).

Per una mappa di tensione **continua** si usa il recupero nodale mediato sul
patch, in coordinate globali:

```python
ns = res.shell_nodal_stresses(side="top")   # o "bot"
ns[node]["sigma"]   # tensore 3x3 globale mediato al nodo
ns[node]["s1"], ns[node]["s2"], ns[node]["s3"]   # principali (decrescenti)
ns[node]["svm"]     # von Mises
```

Ogni elemento contribuisce con la propria tensione (peso = area) sui nodi che
condivide; le principali nodali sono gli autovalori del tensore mediato.
