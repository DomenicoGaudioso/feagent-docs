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
from feagent import VariableSection

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

### Inclined supports (rotated support axes)

For a roller on an inclined sliding plane, rotate the nodal DOF basis with a
3×3 matrix (rows = local support axes in global coordinates); restraints on
that node then act on the **local** DOFs:

```python
import numpy as np
c, s = np.cos(np.pi/4), np.sin(np.pi/4)
R = np.array([[c, s, 0], [-s, c, 0], [0, 0, 1]])   # 45° plane in X-Y
m.support(3, axes=R, uy=True)     # restrain the direction normal to the plane
# equivalent: m.set_support_axes(3, R); m.support(3, uy=True)
```

Reactions are available in global axes via `res.reactions(n)` and in the
support axes via `res.reactions_support_frame(n)`.

## Kinematic constraints (rigid links, equalDOF, diaphragms)

Multipoint constraints via **master–slave elimination** (transformation
method, Cook et al. 2002 ch. 13): exact, no penalty parameters, applied to
the mass matrix (modal) and geometric stiffness (buckling) as well; dense
and sparse paths coincide.

```python
m.add_rigid_link(master, slave)                  # full rigid-body kinematics
m.add_rigid_link(master, slave, dofs=["ux","uy","uz"])   # translations only
m.add_equal_dof(a, b, dofs=["ux", "uy"])        # u_b = u_a on chosen DOFs
m.add_rigid_diaphragm(100, [11, 12, 13], plane="xy")     # rigid floor
```

- the rigid link enforces `u_s = u_m + θ_m × r`, `θ_s = θ_m` with `r = x_s − x_m`;
- the diaphragm ties the 3 in-plane DOFs (2 translations + rotation about
  the normal: `xy` → ux, uy, rz; `xz` → ux, uz, ry; `yz` → uy, uz, rx);
- slave-of-slave chains are resolved automatically; duplicated slaves,
  cycles and ground restraints/settlements on slave DOFs raise explicit
  errors;
- if a diaphragm master is a fictitious node, restrain its out-of-plane
  DOFs with `fix` (e.g. `m.fix(100, ["uz", "rx", "ry"])` for `plane="xy"`).

## Solution

```python
res = m.solve()                   # dense solver (default)
res = m.solve(sparse=True)        # sparse solver (large models)
res = m.solve(cases=["G", "Q"])    # specific load cases
```

See [Sparse Solver](en-12-sparse-solver.html) for performance details.
