---
layout: default
title: "03 - Structural Model"
parent: English
nav_order: 3
---

# 03 - Structural Model

## Nodes

Each node has 6 degrees of freedom (DOFs): `[ux, uy, uz, rx, ry, rz]`.

```python
m.add_node(id, x, y, z)   # id = integer identifier, coordinates in meters
```

Example:
```python
m.add_node(1, 0, 0, 0)    # origin
m.add_node(2, 5, 0, 0)    # 5 m along X
m.add_node(3, 5, 3, 0)    # 5 m in X, 3 m in Y (X-Y plane)
m.add_node(4, 0, 0, 4)    # 4 m in Z (vertical)
```

## Materials

```python
mat = Material(E=210e9, nu=0.3, alpha=1.2e-5)   # steel
mat = Material(E=30e9, nu=0.2, alpha=1.0e-5)       # concrete
```

| Parameter | Description | Default |
|-----------|-------------|---------|
| `E` | Young's modulus [Pa] | required |
| `nu` | Poisson's ratio | 0.3 |
| `alpha` | Thermal expansion coefficient [1/°C] | 0.0 |
| `G` | Shear modulus [Pa] | computed from E and nu |
| `rho` | Density [kg/m³] | 0.0 (not used) |

## Sections

```python
# Rectangular section b×h = 0.30×0.50 m
b, h = 0.30, 0.50
sec = Section(A=b*h, Iy=h*b**3/12, Iz=b*h**3/12, J=b*h*(b**2+h**2)/12)
```

| Parameter | Description | Required |
|-----------|-------------|----------|
| `A` | Cross-sectional area | yes |
| `Iy` | Moment of inertia about y (bending in x-z plane) | yes |
| `Iz` | Moment of inertia about z (bending in x-y plane) | yes |
| `J` | Torsional constant | yes |
| `Asy`, `Asz` | Effective shear areas (Timoshenko) | no |
| `h_y`, `h_z` | Section heights (for thermal loads) | no |

**Convention**: `Iy` → bending in x-z plane, `Iz` → bending in x-y plane.
For a rectangular section b×h with strong axis horizontal: `Iz = b·h³/12` (strong), `Iy = h·b³/12` (weak).

## Elements

### Prismatic element (Euler-Bernoulli / Timoshenko)

```python
m.add_beam(id, node_i, node_j, material, section, ...)
```

Optional parameters:
- `ref_vector` — vector for section orientation (see [Orientation](en-08-section-orientation.html))
- `roll` — section rotation angle [rad]
- `shear=True` — activate Timoshenko formulation (requires Asy, Asz)
- `releases_i`, `releases_j` — list of released DOFs (see [Releases](en-06-timoshenko-releases.html))

### Tapered element (variable section)

```python
from beamfeapy import VariableSection

# Method 1: continuous function
vs = VariableSection.rectangular(b=0.30, h=lambda xi: 0.70*(1-0.6*xi))
m.add_tapered_beam(id, ni, nj, mat, vs)

# Method 2: sections at ends (linear interpolation)
m.add_section("root", A=1.5e-2, Iy=5e-5, Iz=9e-5, J=4e-5)
m.add_section("tip", A=0.7e-2, Iy=1.2e-5, Iz=2e-5, J=1e-5)
m.add_tapered_beam(id, ni, nj, mat, section_i="root", section_j="tip")

# Method 3: intermediate stations
m.add_tapered_beam(id, ni, nj, mat, stations={0.0: "root", 0.5: "mid", 1.0: "tip"})
```

See the dedicated guide: [Tapered Section](en-05-tapered-section.html).

## Supports

```python
m.fix(node)                         # fixed: all 6 DOFs restrained
m.pin(node)                          # pin: ux, uy, uz restrained
m.support(node, ux=True, uy=True)    # custom: only specified DOFs
```

| Method | Restrained DOFs | Typical use |
|--------|-----------------|-------------|
| `fix(n)` | ux,uy,uz,rx,ry,rz | Fixed support |
| `pin(n)` | ux,uy,uz | Spherical hinge |
| `support(n,...)` | custom | Roller, slider, etc. |

Examples of `support`:
```python
m.support(1, ux=True, uy=True, uz=True, rx=True)  # 3D pin (4 DOFs)
m.support(2, uy=True, uz=True, rx=True)             # roller (3 DOFs, ux free)
m.support(3, uy=True)                                # vertical only (slider)
```

## Solution

```python
res = m.solve()                   # dense solver (default)
res = m.solve(sparse=True)        # sparse solver (large models)
res = m.solve(cases=["G", "Q"])    # specific load cases
```

See [Sparse Solver](en-12-sparse-solver.html) for performance details.
