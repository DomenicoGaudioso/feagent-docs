---
layout: default
title: "44 - Solid elements: formulation"
parent: English
nav_order: 44
---

# 44 - Solid elements: formulation

This page carries over the **volumfeapy** documentation (shape functions and
element types), now that the solid solver is part of feagent, and describes
how the elements of `feagent.solid` are built. Usage is in
[chapter 43](en-43-solid-elements.html), case studies in
[chapter 45](en-45-solid-case-studies.html).

## Common isoparametric scheme

All elements interpolate geometry and displacements with the same shape
functions *N*ᵢ(ξ) in the natural coordinates of the reference element
(Zienkiewicz, Taylor & Zhu 2013, ch. 6; Bathe 2014, sec. 5.3):

```
x(ξ) = Σ Nᵢ(ξ) xᵢ          u(ξ) = Σ Nᵢ(ξ) uᵢ
J = ∂x/∂ξ = (∂N/∂ξ) X       ∂N/∂x = J⁻¹ ∂N/∂ξ
ε = B u   (Voigt: εxx, εyy, εzz, γxy, γyz, γxz)
K = ∫ Bᵀ D B dV = Σg wg Bgᵀ D Bg det Jg
```

`D` is the 6x6 isotropic elastic matrix with engineering shear strains
(γ = 2ε). Each element declares its shape functions, their derivatives and two
quadrature rules: one for stiffness and a higher-order one for masses and body
forces. Mirrored numbering (negative Jacobian at all points) is accepted and
integrated with |det J|; elements whose Jacobian changes sign (distorted or
degenerate) are rejected.

## Hex8

Trilinear hexahedron, (ξ, η, ζ) ∈ [−1, 1]³:

```
Nᵢ = ⅛ (1 + ξᵢ ξ)(1 + ηᵢ η)(1 + ζᵢ ζ)
```

| Node | ξᵢ | ηᵢ | ζᵢ |
|---|---|---|---|
| 1 | −1 | −1 | −1 |
| 2 | +1 | −1 | −1 |
| 3 | +1 | +1 | −1 |
| 4 | −1 | +1 | −1 |
| 5 | −1 | −1 | +1 |
| 6 | +1 | −1 | +1 |
| 7 | +1 | +1 | +1 |
| 8 | −1 | +1 | +1 |

![Hex8 shape functions](images/solidi/shape_functions_hex8.png)
*Figure 1 — The eight trilinear shape functions on the mid-plane ζ = 0.*

Stiffness with 2x2x2 Gauss. The standard Hex8 cannot represent pure bending
and locks: a cantilever with 2 elements through the depth gets 86 % of the
exact deflection.

### Incompatible-mode Hex8

With `incompatible=True` the three Wilson modes (1 − ξ²), (1 − η²), (1 − ζ²)
are added in each direction, i.e. 9 internal parameters α:

```
ε = B u + G α          G built with J₀ (centroid) and the factor det J₀ / det J
[Kuu Kuα; Kαu Kαα] → K = Kuu − Kuα Kαα⁻¹ Kαu
```

Taylor's correction (Taylor, Beresford & Wilson 1976) makes the element pass
the patch test on distorted meshes; α is condensed at element level and
recovered for stresses, thermal loads included. It is the equivalent of C3D8I:
pure bending on regular elements is exact.

## Tet4

Linear tetrahedron in volume coordinates (L₁, L₂, L₃, L₄), with
L₁ = 1 − r − s − t: Nᵢ = Lᵢ. Constant strain, exact one-point stiffness. Stiff
in bending: use fine meshes or, better, Tet10.

![Tet4 shape functions](images/solidi/shape_functions_tet4.png)
*Figure 2 — The four linear shape functions of the tetrahedron.*

## Tet10

Quadratic tetrahedron: vertices Nᵢ = Lᵢ(2Lᵢ − 1), edge nodes N = 4 Lⱼ Lₖ, in the
order (1-2), (2-3), (3-1), (1-4), (2-4), (3-4). Four-point stiffness (exact for
straight edges), masses with Stroud's conical product. Isoparametric: edge
nodes may lie off the segment (curved edges from Gmsh). The Gmsh import
reorders edge nodes by position, since Gmsh swaps the last two.

## Wedge6

Linear triangle (r, s) times linear extrusion in ζ: base 1-2-3 with
[L₁, L₂, L₃](1 − ζ)/2, top 4-5-6 with [L₁, L₂, L₃](1 + ζ)/2. 3 x 2 point
stiffness. Volume and Jacobian hold for any orientation (in the original
branch the Jacobian was transposed and the volume valid only for a vertical
extrusion).

## Pyramid5

Degenerate hexahedron with the four top nodes collapsed into the apex
(Zienkiewicz sec. 6.6; Bedrosian 1992):

```
N₁…₄ = ⅛ (1 + ξᵢ ξ)(1 + ηᵢ η)(1 − ζ)     N₅ = (1 + ζ)/2
```

It satisfies partition of unity (the original branch did not: the sum was
1.5 − ζ/2), is conforming with Hex8 on the square base and with Tet4 on the
triangular faces, and passes the patch test. Apex stresses, where the Jacobian
vanishes, are evaluated as the limit along the axis.

## Equivalent loads and masses

| Quantity | Formula | Notes |
|---|---|---|
| Body force | f = ∫ Nᵀ b dV | consistent; Tet10 vertices get negative forces, as expected |
| Face load | f = ∫ Nᵀ (q − p n) dA | outward normal from geometry; tri3, tri6, quad4 |
| Thermal | f = ∫ Bᵀ D ε_th dV, ε_th = α T(ξ) (1,1,1,0,0,0) | T uniform or interpolated from nodes |
| Consistent mass | M = ∫ ρ Nᵀ N dV | |
| Lumped mass | HRZ diagonal | consistent diagonal scaled to the total mass: no negative masses |

## Stresses

Stresses are evaluated at the requested natural point (centroid, Gauss points,
nodes) with the thermal part removed: σ = D (B u + G α − ε_th). The nodal map
(`solid_nodal_stresses`) volume-averages the values computed at the node by
adjacent elements; von Mises and principal stresses come from the averaged
tensor.

## Defects of the volumfeapy branch fixed during the merge

* Pyramid5 without partition of unity (wrong results).
* Wedge6 with a transposed Jacobian and a volume valid only for vertical extrusion.
* Face pressure declared but never assembled.
* Tet10 from Gmsh with two edge nodes swapped.
* Equal-share lumped masses (wrong for Tet10): now HRZ.
