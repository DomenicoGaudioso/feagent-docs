---
layout: default
title: "08 - Section Orientation"
parent: English
nav_order: 8
---

# 08 - Section Orientation

In a 3D model, the orientation of the cross-section is fundamental for determining which element axes correspond to `Iy` and `Iz`.

## Local axes

For an element going from node i to node j:

- **local x**: from node i to node j (beam axis)
- **local y**: determined by `ref_vector` or `roll`
- **local z**: `z = x × y` (cross product)

**Convention**:
- `Iz` → bending in x-y plane (curvature along y)
- `Iy` → bending in x-z plane (curvature along z)

## Automatic selection (default)

If neither `ref_vector` nor `roll` is specified:

```python
m.add_beam(1, 1, 2, mat, sec)   # default
```

- If the beam axis is **nearly horizontal** (|e_x,z| < 0.999), local y = global Z
- If the axis is **nearly vertical** (|e_x,z| > 0.999), local y = global Y

This works for most cases.

## ref_vector

The `ref_vector` defines the local x-y plane: `e_y = ref_vector × e_x`:

```python
# Portal frame in x-z plane (Z = vertical)
m.add_beam(2, 2, 3, mat, sec, ref_vector=(0, 0, 1))

# Inclined beam: local y along (1, 1, 0)
m.add_beam(3, 3, 4, mat, sec, ref_vector=(1, 1, 0))
```

**Warning**: `ref_vector` must not be parallel to the beam axis!

## Roll angle

The `roll` parameter rotates the section about the local x-axis (in radians):

```python
# 30° rotation (pi/6 rad)
m.add_beam(1, 1, 2, mat, sec, roll=np.radians(30))
```

`roll` is applied after the default orientation. Useful for inclined sections.

## Example: 3D portal frame

```python
# Vertical columns: default OK
m.add_beam(1, 1, 2, mat, col_sec)
# Horizontal beam: ref_vector=(0,0,1) for y=Z vertical
m.add_beam(2, 2, 3, mat, bm_sec, ref_vector=(0, 0, 1))
# Right column (downward): default OK
m.add_beam(3, 3, 4, mat, col_sec)
```
