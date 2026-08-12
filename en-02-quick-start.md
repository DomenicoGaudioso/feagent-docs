---
layout: default
title: "02 - Quick Start"
parent: English
nav_order: 2
---

# 02 - Quick Start

Your first model: a cantilever beam with a tip load.

```python
from beamfeapy import Model, Material, Section

# 1. Create the model
m = Model()

# 2. Add nodes
m.add_node(1, 0, 0, 0)   # fixed end
m.add_node(2, 4, 0, 0)   # tip (4 m long)

# 3. Define material and section
mat = Material(E=210e9, nu=0.3, alpha=1.2e-5)   # steel
sec = Section(A=1e-2, Iy=2e-5, Iz=3e-5, J=1e-5)  # generic section

# 4. Add beam element
m.add_beam(1, 1, 2, mat, sec)

# 5. Apply constraints and loads
m.fix(1)                        # fixed support (6 DOFs)
m.add_nodal_load(2, Fy=-10000)  # 10 kN force in -Y

# 6. Solve
res = m.solve()

# 7. Read results
print(res.displacements(2))   # [ux, uy, uz, rx, ry, rz]
print(res.reactions(1))       # [Fx, Fy, Fz, Mx, My, Mz]
```

## Expected result

For a cantilever with tip load: `uy = P·L³ / (3·E·Iz)`

```python
uy_theory = -10000 * 4**3 / (3 * 210e9 * 3e-5)
print(f"uy FEM     = {res.displacement(2, 'uy'):.6e} m")
print(f"uy theory  = {uy_theory:.6e} m")
# uy FEM     = -1.015873e-02 m
# uy theory  = -1.015873e-02 m
```

## Next steps

- [Loads](en-04-loads.html) — distributed, concentrated, thermal, settlements, prestress
- [Tapered section](en-05-tapered-section.html) — tapered element
- [Post-Processing](en-09-post-processing.html) — internal forces and diagrams
