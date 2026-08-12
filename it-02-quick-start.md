---
layout: default
title: "02 - Quick Start"
parent: Italiano
nav_order: 2
---

# 02 - Quick Start

Il primo modello: una mensola con carico in punta.

```python
from beamfeapy import Model, Material, Section

# 1. Crea il modello
m = Model()

# 2. Aggiungi nodi
m.add_node(1, 0, 0, 0)   # incastro
m.add_node(2, 4, 0, 0)   # punta (4 m di lunghezza)

# 3. Definisci materiale e sezione
mat = Material(E=210e9, nu=0.3, alpha=1.2e-5)   # acciaio
sec = Section(A=1e-2, Iy=2e-5, Iz=3e-5, J=1e-5)  # sezione generica

# 4. Aggiungi l'elemento trave
m.add_beam(1, 1, 2, mat, sec)

# 5. Applica vincoli e carichi
m.fix(1)                        # incastro (6 GdL)
m.add_nodal_load(2, Fy=-10000)  # forza di 10 kN in -Y

# 6. Risolvi
res = m.solve()

# 7. Leggi i risultati
print(res.displacements(2))   # [ux, uy, uz, rx, ry, rz]
print(res.reactions(1))       # [Fx, Fy, Fz, Mx, My, Mz]
```

## Risultato atteso

Per una mensola con carico in punta: `uy = P·L³ / (3·E·Iz)`

```python
uy_teoria = -10000 * 4**3 / (3 * 210e9 * 3e-5)
print(f"uy FEM     = {res.displacement(2, 'uy'):.6e} m")
print(f"uy teoria  = {uy_teoria:.6e} m")
# uy FEM     = -1.015873e-02 m
# uy teoria  = -1.015873e-02 m
```

## Prossimi passi

- [Carichi](it-04-loads.html) — distribuiti, concentrati, termici, cedimenti, precompressione
- [Sezione variabile](it-05-tapered-section.html) — elemento tapered
- [Post-Processing](it-09-post-processing.html) — azioni interne e diagrammi