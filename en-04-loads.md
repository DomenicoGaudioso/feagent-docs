---
layout: default
title: "04 - Loads"
parent: English
nav_order: 4
---

# 04 - Loads

feagent supports all major load types for static analysis of frame structures.

## Nodal loads

Forces and moments applied directly at nodes (global system):

```python
m.add_nodal_load(node, Fx=1000, Fy=-5000, Fz=0, Mx=0, My=0, Mz=3000, case="G")
```

All parameters are optional except the node. Components are in global coordinates.

## Distributed loads

Loads per unit length on elements. Includes uniform, partial, and trapezoidal:

```python
# Uniform over the entire element
m.add_distributed_load(elem, "fy", -3000.0)

# Partial (from x=1m to x=3m)
m.add_distributed_load(elem, "fy", -5000.0, a=1.0, b=3.0)

# Trapezoidal full (q_i at x=0, q_j at x=L)
m.add_distributed_load(elem, "fy", 0, -8000.0)

# Trapezoidal partial (from x=2m to x=5m)
m.add_distributed_load(elem, "fy", -2000.0, -8000.0, a=2.0, b=5.0)

# In global coordinates
m.add_distributed_load(elem, "fy", -3000.0, frame="global")
```

**Components**: `fx`, `fy`, `fz` (forces), `mx`, `my`, `mz` (distributed moments).  
**Parameters**: `q_i` (initial value), `q_j` (final value, default = q_i), `a`, `b` (span limits), `frame` (`"local"` or `"global"`).

## Concentrated loads in span

Forces and moments applied at an internal point of the element:

```python
# Force at x = 0.35·L
m.add_concentrated_load(elem, 0.35, Fy=-50000.0)

# Moment at x = 0.70·L
m.add_concentrated_load(elem, 0.70, Mz=80000.0)

# In global coordinates at midspan
m.add_concentrated_load(elem, 0.5, Fz=-20000.0, frame="global")
```

`xi ∈ [0, 1]` is the normalized abscissa (0 = node i, 1 = node j).

## Thermal loads

```python
# Uniform temperature increase
m.add_thermal_load(elem, dT_axial=20.0)

# Gradient along z (requires section.h_z)
m.add_thermal_load(elem, dT_axial=20.0, dT_grad_z=15.0)

# Gradient along y
m.add_thermal_load(elem, dT_grad_y=12.0)
```

### Generic thermal profile (EN 1991-1-5)

```python
# Function T(s), with s from centroid in [-h/2, +h/2]
m.add_thermal_profile(elem, lambda s: 15*(0.5 + s/0.3), axis="z")

# Discrete points [(s, T)]
m.add_thermal_profile(elem, [(-0.15, 0), (0.05, 2.5), (0.15, 15)],
                       axis="z", width=0.30)
```

For self-equilibrating stresses (eigenstress):
```python
from feagent.loads import ThermalProfile
tp = ThermalProfile(elem, profile, axis="z", width=B)
sigma = tp.eigenstress(element, s)  # self-equilibrating stress at height s
```

## Settlements

```python
m.add_settlement(node, "uy", -0.005)   # vertical settlement of 5 mm
m.add_settlement(node, "rz", 0.001)     # imposed rotation
```

The DOF can be: `ux`, `uy`, `uz`, `rx`, `ry`, `rz`.

## Prestress

### Equivalent load method (internal cable)

```python
# Parabolic cable: sag, zero eccentricity at ends
m.add_prestress(elem, P=2.0e6, sag=0.35)

# Straight eccentric cable
m.add_prestress(elem, P=1.5e6, e_i=0.20, e_j=0.20, plane="y")

# Generic eccentricity profile
m.add_prestress(elem, P=1.0e6, profile=lambda xi: 0.3*(1-(2*xi-1)**2))
```

In a statically determinate structure: primary moments M = P·e, zero reactions. In indeterminate: secondary moments are computed automatically.

### From 3D cable geometry

```python
# Cable defined as 3D polyline
pts = [(0, 0.3, 0), (5, -0.05, 0), (10, 0.3, 0)]
m.add_cable_prestress(P=3.0e6, points=pts)

# Specify affected elements (optional)
m.add_cable_prestress(P=3.0e6, points=pts, elements=[1, 2])
```

The cable is defined by its global coordinates and tension P. Anchor and deviation forces are computed and applied automatically.

## Assignment to Load Cases

Each load can have a `case`:

```python
m.add_nodal_load(2, Fy=-10000, case="G")          # permanent
m.add_distributed_load(1, "fy", -5000, case="G")    # permanent
m.add_nodal_load(2, Fx=30000, case="Q")             # variable
m.add_thermal_load(1, dT_axial=20, case="T")         # thermal

m.load_cases()                    # → ['G', 'Q', 'T']
res = m.solve(cases=["G", "Q"])    # combination
res = m.solve(cases="G")           # single case
res = m.solve()                     # all loads
```

## Illustrated examples

**Prestress from parabolic cable** (primary moment `M = P·sag`):

![](images/cs7_Mz.png)

**Prestress from cable geometry** on 2-span continuous beam
(with hyperstatic secondary moments):

![](images/cs8_Mz.png)

Full gallery: [Case Studies](en-16-case-studies-gallery.html).

See [Load Cases](en-07-load-cases.html) for details.

## Automatic self-weight

`add_self_weight` applies the self-weight of the **whole model** into a load
case, with consistent load vectors per element category:

```python
mat = Material(E=210e9, nu=0.3, gamma=78.5e3)   # unit weight [N/m^3]
W = m.add_self_weight(case="G1", direction=(0, 0, -1))   # returns weight [N]
```

- **beams** (tapered included): distributed load `γ·A(x)` registered as a
  `DistributedLoad` → it enters diagrams and `internal_forces`;
- **trusses**: half weight on each node;
- **shells** Q4/T3: tributary nodal loads `γ·t·A` plus the optional
  `extra_weight_per_area` surcharge (smeared ribs:
  `ShellSectionOrthotropic.from_stiffeners(gamma_rib=...)`);
- **cables** are excluded (their weight is intrinsic in `solve_nonlinear`).

If `Material.gamma` is missing, `rho*g` is used; if both are missing the
error is explicit (`gamma=0` deliberately excludes a material, e.g.
fictitious rigid elements). Use `direction=(0, -1, 0)` in Y-up models.

## Shell surface loads

Normal pressure (Q4 and T3) and generic surface loads with **consistent**
equivalent nodal forces (for the linear T3: `q·A/3` per node — resultant and
centroid exact even on irregular meshes):

```python
m.add_shell_pressure(1, -2000.0, case="Q")                # along local +e3
m.add_shell_surface_load(1, qz=-2000.0, frame="local")     # equivalent
m.add_shell_surface_load(1, qx=800.0, case="W")            # global (wind)
m.add_shell_surface_load(1, qz=-1200.0, projected=True)    # snow: intensity
                                                            # on projected area
```

With `projected=True` (global frame only) the intensity refers to the area
projected perpendicular to the load: on an inclined plane the resultant is
`q·A·cosθ`.

## Shell thermal loads

Uniform change `dT` at the mid-plane and/or gradient `dT_grad` (top minus
bottom surface across the thickness t):

```python
m.add_shell_thermal(1, dT=25.0, case="T")            # uniform expansion
m.add_shell_thermal(1, dT_grad=15.0, case="T")       # thermal curvature
```

Imposed strains `ε₀ = α·dT·[1,1,0]`, `κ₀ = α·dT_grad/t·[1,1,0]`
(Boley & Weiner 1960) and equivalent loads including the
**membrane–bending coupling B** of eccentric sections (`from_stiffeners`):
on a stiffened plate restrained in-plane a uniform `dT` produces curvature.
In stress recovery (`res.shell_forces`, `res.shell_stresses`) the thermal
part is subtracted: `N = A(ε−ε₀) + B(κ−κ₀)`, etc. Requires `Material.alpha`.
