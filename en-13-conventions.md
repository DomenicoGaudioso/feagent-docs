---
layout: default
title: "13 - Conventions"
parent: English
nav_order: 13
---

# 13 - Conventions

## Nodal degrees of freedom

Each node has 6 DOFs in order: `[ux, uy, uz, rx, ry, rz]`

- `ux`, `uy`, `uz`: translations along global axes X, Y, Z
- `rx`, `ry`, `rz`: rotations about global axes X, Y, Z

## Element local axes

For an element going from node i to node j:

- **local x**: from node i to node j (beam axis)
- **local y**: perpendicular to x, in the plane defined by `ref_vector`
- **local z**: `z = x × y` (cross product, right-hand rule)

**Inertia moment conventions**:
- `Iy` → bending in x-z plane (strong axis, section "laid flat")
- `Iz` → bending in x-y plane (weak axis, section "standing up")

For a rectangular section b×h (b horizontal, h vertical):
- `Iy = h·b³/12` (weak axis)
- `Iz = b·h³/12` (strong axis)

## Internal force sign convention

- **Axial force N**: positive in tension
- **Shear Vy, Vz**: positive in the positive direction of the corresponding local axis
- **Torsional moment T**: positive counterclockwise (viewed from +x)
- **Bending moments My, Mz**: European convention (negative moment at the extrados)

## Distributed loads

- Components `fx`, `fy`, `fz`: forces per unit length
- Components `mx`, `my`, `mz`: moments per unit length
- Positive in the positive directions of local/global axes

## Units

The system is **unit-agnostic**: the user chooses units as long as they are consistent. Example with SI:

| Quantity | Unit |
|----------|------|
| Length | m |
| Force | N |
| Pressure | Pa |
| Temperature | °C |
| Moment | Nm |

## Default orientation

If `ref_vector` is not specified:
- **Nearly horizontal** elements (|e_x[2]| < 0.999): local y ≈ global Z
- **Nearly vertical** elements (|e_x[2]| > 0.999): local y ≈ global Y
