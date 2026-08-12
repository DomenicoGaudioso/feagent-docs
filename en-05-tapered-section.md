---
layout: default
title: "05 - Tapered Section"
parent: English
nav_order: 5
---

# 05 - Tapered Section (Variable Cross-Section)

The tapered element uses the **force-based** formulation (Friedman & Kosmatka 1992; Eisenberger 1991) which provides **exact** stiffness for any section variation. **A single element replaces the discretization** into multiple prismatic elements.

## VariableSection

```python
from beamfeapy import VariableSection

# Rectangular section with variable height
vs = VariableSection.rectangular(b=0.30, h=lambda xi: 0.70*(1-0.6*xi))

# Generic section (all properties as functions of xi)
vs = VariableSection(A=lambda xi: 0.01*(1+xi),
                     Iy=lambda xi: 2e-5*(1+xi)**2,
                     Iz=lambda xi: 3e-5*(1+xi)**2,
                     J=lambda xi: 1e-5*(1+xi))
```

`xi ∈ [0, 1]` where 0 = node i (root), 1 = node j (tip).

## Three ways to define the variable section

### Method 1: VariableSection with function

```python
vs = VariableSection.rectangular(b=0.30, h=lambda xi: 0.70*(1-0.6*xi))
m.add_tapered_beam(id, ni, nj, mat, vs)
```

### Method 2: Sections at ends (linear interpolation)

```python
m.add_section("root", A=1.2e-2, Iy=4e-5, Iz=6e-5, J=3e-5)
m.add_section("tip", A=0.6e-2, Iy=1e-5, Iz=1.5e-5, J=0.8e-5)
m.add_tapered_beam(id, ni, nj, mat, section_i="root", section_j="tip")
```

### Method 3: Intermediate stations

```python
m.add_section("root", A=1.2e-2, Iy=4e-5, Iz=6e-5, J=3e-5)
m.add_section("mid", A=1.0e-2, Iy=2.5e-5, Iz=4e-5, J=2e-5)
m.add_section("tip", A=0.6e-2, Iy=1e-5, Iz=1.5e-5, J=0.8e-5)
m.add_tapered_beam(id, ni, nj, mat,
                    stations={0.0: "root", 0.4: "mid", 1.0: "tip"})
```

Between stations the interpolation is piecewise linear. Each station becomes a *breakpoint* for integration.

## Timoshenko + Tapered

```python
m.add_tapered_beam(id, ni, nj, mat, vs, shear=True,
                   Asy=lambda xi: 5/6*A(xi), Asz=lambda xi: 5/6*A(xi))
```

## Advantages

- **Exactness**: stiffness computed exactly (no discretization error)
- **Efficiency**: one element instead of dozens of prismatic elements
- **Loads**: distributed (uniform, partial, trapezoidal), concentrated, thermal — all supported
- **Convergence**: FEM results match the reference solution to within 1e-3 %

## Illustrated example (tapered cantilever 0.70 → 0.30 m)

| Deformed shape | Moment Mz |
|---|---|
| ![](images/cs5_deformata.png) | ![](images/cs5_Mz.png) |

See also [Case Studies](en-16-case-studies-gallery.html).
