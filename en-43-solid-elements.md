---
layout: default
title: "43 - 3D solid elements"
parent: English
nav_order: 43
---

# 43 - 3D solid elements

feagent also solves models with **solid** (volumetric) elements, in the same
`Model` as beams and shells. The elements come from the former `volumfeapy`
branch, now merged into the library (module `feagent.solid`). The element
formulation is in [chapter 44](en-44-solid-formulation.html), the case studies in
[chapter 45](en-45-solid-case-studies.html).

| Element | Nodes | Formulation | Use it for |
|---|---|---|---|
| `Hex8` | 8 | trilinear, 2x2x2 Gauss | smooth stress states, structured meshes |
| `Hex8` with `incompatible=True` | 8 | Wilson-Taylor incompatible modes, condensed | **bending** with few elements through the thickness (walls, slabs, girder segments) |
| `Tet4` | 4 | linear, constant strain | filling; stiff in bending, needs fine meshes |
| `Tet10` | 10 | quadratic isoparametric | **arbitrary geometry from Gmsh**: the most reliable element |
| `Wedge6` | 6 | triangle times extrusion | layers extruded from triangular meshes |
| `Pyramid5` | 5 | degenerate hexahedron | transition between hexahedra and tetrahedra |

All elements are isoparametric (Zienkiewicz, Taylor & Zhu 2013, ch. 6; Bathe
2014, sec. 5.3) and pass the constant-strain patch test on distorted meshes.
The standard Hex8 shear-locks in bending (a cantilever with 2 elements through
the depth gets 86 % of the exact deflection); incompatible modes reach 97 %
and are exact in pure bending on regular elements.

## First example: cantilever

```python
from feagent import Model, Material
from feagent.solid_mesh import box_hex, nodes_where, boundary_faces_where

m = Model()
steel = Material(E=210e9, nu=0.3, rho=7850)
box_hex(m, 2.0, 0.2, 0.4, 10, 1, 4, steel, incompatible=True)   # L x b x h

for n in nodes_where(m, lambda x, y, z: x < 1e-9):            # clamped end
    m.fix(n, ["ux", "uy", "uz"])
for e, f in boundary_faces_where(m, lambda x, y, z: abs(x - 2.0) < 1e-9):
    m.add_solid_surface_load(e, f, qz=-1e4 / (0.2 * 0.4))    # 10 kN at the tip

r = m.solve(sparse=True)
print(r.solid_stresses(1))              # stresses at the centroid of element 1
nodal = r.solid_nodal_stresses()        # continuous map: {node: {sxx, ..., von_mises, s1, s2, s3}}
```

## Nodes and faces

A solid node uses only the **3 translational DOFs** of the 6 of a feagent node.
Rotations of nodes connected only to solids have no stiffness and are fixed by
the solver automatically. A moment applied to a solid-only node raises an
error (it would be lost): apply a force couple or connect a beam.

Node ordering (local numbering from 1):

* **Hex8**: bottom 1-2-3-4, top 5-6-7-8 (5 above 1). Mirrored numbering is
  accepted (integration uses the absolute Jacobian); distorted or degenerate
  elements are rejected.
* **Tet10**: 4 vertices, then the edge nodes (1-2), (2-3), (3-1), (1-4), (2-4),
  (3-4). The Gmsh import reorders them by position.
* **Wedge6**: triangle 1-2-3 at the bottom, 4-5-6 at the top. **Pyramid5**:
  base 1-2-3-4 and apex 5.

Faces for loads (0-based): Hex8 0 = bottom, 1 = top, 2..5 = sides; tetrahedra:
face *k* is opposite node *k*+1; Wedge6 0 = bottom, 1 = top, 2..4 sides;
Pyramid5 0 = base, 1..4 triangles. You rarely need them:
`m.solid_boundary_faces()` lists boundary faces and
`boundary_faces_where(m, pred)` selects them by position. The normal is taken
from the geometry, so the face orientation does not matter.

## Loads

| Method | Load |
|---|---|
| `add_nodal_load(n, Fx=..., ...)` | nodal forces |
| `add_solid_pressure(e, face, p)` | pressure, positive pushing into the element |
| `add_solid_surface_load(e, face, qx, qy, qz)` | global traction [F/L²] |
| `add_solid_body_force(e, bx, by, bz)` | body force [F/L³] |
| `add_solid_thermal(e, dT)` | uniform temperature change, or list of nodal values |
| `add_solid_temperature_field(T)` | volume temperature field from `T(x, y, z)` or `{node: T}` |
| `add_self_weight("G1")` | self-weight of solids too (consistent nodal forces) |
| `add_settlement(n, "uz", v)` | imposed displacements |

All loads take `case=` and combine with factors like any other load
(`solve(cases={"G1": 1.35, "Q": 1.5})`, `solve_many`). Equivalent forces are
**consistent** (∫Nᵀ q dA, ∫Nᵀ b dV, ∫Bᵀ D ε_th dV); the thermal part is
subtracted in stress recovery.

## Mixed models with beams and shells: avoid them

Solids, beams and shells can live in the same model, but they **do not
integrate well**: beams and shells have 6 DOFs per node (rotations included),
solids only the 3 translations, so the connection is not consistent.

* A **shared node** transmits forces only: it is a **hinge**. Beam or shell
  moments do not enter the solid; a cantilever beam standing on a single solid
  node is a mechanism. The force concentrated in one node also gives local
  stresses that grow as the mesh is refined.
* **Rigid links** from the beam to all the nodes of a face transmit the moment
  but enforce plane sections: the face is stiffer and stresses near the joint
  are disturbed.

feagent therefore **warns** whenever a model with solids together with beams
or shells is analysed: a `UserWarning` at the start of `solve`, `solve_many`,
`modal`, `buckling`, `solve_pdelta` and `solve_nonlinear`, and the typed
warning `SOLIDI_MISTI` in `Result.warnings` / `Result.warning_details` (with
the number of shared nodes and kinematic links between the parts). If the
structure turns out to be a mechanism, the error message points to the
beam-solid hinge as the likely cause. The warning does not mark the result as
unreliable (`Result.reliable` stays true): it flags it for checking.

When a mixed model is unavoidable, rigid links on the whole face are the least
bad option; check equilibrium at the interface and read solid stresses away
from the joint (at least one section depth):

```python
m.add_node(1000, 2.0, 0.1, 0.2)                 # centre of the end face
for n in nodes_where(m, lambda x, y, z: abs(x - 2.0) < 1e-9):
    m.add_rigid_link(1000, n, dofs=["ux", "uy", "uz"])
m.add_beam(1, 1000, 1001, steel, section)       # the beam continues the solid
```

## Analyses

* Linear **static**, dense and sparse, combinations, `solve_many`, kinematic
  constraints.
* **Modal**: solid self-mass (`rho`) is included both lumped (HRZ
  diagonalisation, no negative masses on Tet10) and consistent
  (`mass="consistent"`, which needs a positive definite stiffness, i.e. no
  free rigid-body motion).
* **P-Delta and buckling**: solids contribute their elastic stiffness only (no
  geometric stiffness of their own).

## Meshing

`feagent.solid_mesh`: `box_hex`, `structured_hex` (hexahedral grids on any map
`(u, v, w) -> (x, y, z)`, blocks welded on coincident nodes with `tol > 0`),
`from_gmsh` (imports the active Gmsh 3D mesh, also into a model that already
holds beams and shells; `physical_materials` per volume), `mesh_box_tet`,
`nodes_where`, `boundary_faces_where`. Install Gmsh with
`pip install "feagent[mesh]"`.

## Results and plots

`Result.solid_stresses(e, at="centroid" | "gauss" | "nodes")`,
`Result.solid_nodal_stresses()`, `Result.max_von_mises()`,
`Result.solid_displacements(e)`; plots `plot_solid_mesh`,
`plot_solid_deformed`, `plot_solid_stress`, `plot_solid_mode` in
`feagent.plotting`.

## Validation

* MacNeal-Harder patch tests with a shifted inner node (Hex8, incompatible
  Hex8, Tet4, Wedge6) and curved-edge Tet10: exact constant stress.
* Tension, hydrostatic pressure, free and restrained thermal expansion, free
  thermal bending: exact; self-weight column within 0.2 %.
* Cantilever: Tet10 99 %, incompatible Hex8 97 % of the Timoshenko solution;
  cantilever frequencies within 3 %.
* **NAFEMS LE10** (thick plate under pressure, σyy at D = −5.38 MPa): Tet10
  −5.36 MPa (0.4 %), incompatible Hex8 −5.51 MPa (2.5 %); report in
  `validation/nafems/LE10`.
* **NAFEMS LE11** (solid of revolution under a temperature field, σzz at A =
  −105 MPa): −104.5 MPa (0.4 %) with incompatible-mode Hex8;
  `validation/nafems/LE11`.
* **NAFEMS FV42** (thick hollow sphere, radial vibration): first five modes
  within 0.35 % of the exact solution; `validation/nafems/FV42`.
* **NAFEMS FV52** with the solid model: both elements agree (44 Hz on the
  first mode, 4 % below the closed-form thick-plate value); see
  `validation/nafems/FV52`.

Tests: `tests/test_solid.py`.

## Current limits

* Inconsistent coupling with beams and shells (see above): `SOLIDI_MISTI`
  warning.
* Exporters to other programs, the Excel workbook, the HDF5 file and
  `feagent gui` do not handle solids yet: exports and saves warn how many
  elements are left out.
* Linear elastic isotropic material.
