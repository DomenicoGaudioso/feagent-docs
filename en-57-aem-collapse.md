---
layout: default
title: "57 - AEM collapse simulation (Applied Element Method)"
parent: English
nav_order: 57
---

# 57 - AEM collapse simulation (Applied Element Method)

`feagent.aem` is the **Applied Element Method** solver (Meguro & Tagel-Din,
2000): the structure is discretised into rigid bodies connected by springs
that crack, yield and break; detached bodies become debris that collide
with each other and settle on the ground. Use it for **progressive
collapse** (column removal per UFC 4-023-03), concrete fracture, impact and
blast.

Axis convention: **Z up**, default gravity (0, 0, -9.81).

## Models

* **Frames** (`BeamElement3D` only): each beam is split into
  `segments_per_beam` rigid bodies (default 6). Joints carry a *spring set*
  (N, V1, V2, T, M1, M2) in the member axes, whose stiffness is the series
  of the two facing half-lengths (`EA/(l/2)`, `GA_s/(l/2)`, `EI/(l/2)`,
  `GJ/(l/2)`); at nodes with more members the star of half springs is
  replaced exactly by the mesh of pair springs (`C_ij = K_i S^-1 K_j`),
  recomputed after a removal. Supports connect the body to the ground with
  its half spring, on the blocked global DOFs only. Laws: elastic-brittle
  or elastic-plastic (`plastic=True`, failure at `ductility` times yield);
  capacities `fy A`, `fy A_s/sqrt(3)`, `fy W_t/sqrt(3)`, `fy W_pl`. A joint
  that loses its axial force, or whose points separate by more than a
  quarter of the segment, detaches completely (failure `SEP`) and contact
  takes over.
* **Solids** (every feagent type: Hex8, also incompatible, Tet4, Tet10,
  Wedge6, Pyramid5, mixed in one mesh too): one rigid body per element
  with the **exact inertia tensor** of the polyhedron (faces from the
  corner nodes: straight edges); on every shared face a **grid of
  n x n springs** (`springs_per_side`, default 5), normal + 2 tangential,
  with the series stiffness of the two centroid-to-face lengths. No tuning
  factor: the Hex8 cantilever matches the Timoshenko beam within 5 % with
  the 5 x 5 grid (1-2 % with 10 x 10).

Springs work in a frame rotating with the **mean rotation** of the two
bodies; rotations are finite and updated multiplicatively
(`R <- R expm(dt w)`), and the branch of the relative-rotation logarithm is
tracked continuously (a hinge may rotate beyond 180 degrees).

## Concrete (`ConcreteProps`)

* tension: elastic up to `f_t A_t`, then linear softening to the opening
  `w_f = 2 G_f / f_t`: the dissipated energy is `G_f A_t` whatever the mesh;
  secant unloading and reloading (damage with memory);
* compression: full stiffness even with an open crack, elastic-plastic with
  `fc`;
* shear: principal-stress criterion (`sigma_1 >= f_t`, Tagel-Din & Meguro)
  then friction `mu` (default 0.5); a spring without cohesion that slides
  beyond its own cell separates.

Softening must be resolved in time: if the relative opening per step exceeds
`w_f` the spring jumps from elastic to broken in one step, does not
dissipate its fracture energy, and the solver warns about it.

## Loads, masses, scenarios

Self-weight, nodal loads (also by case, `cases=`), **distributed** loads
(trapezoidal and partial too, local or global axes) and **concentrated**
loads on beams, applied to the segments at the load centroid, body forces
and pressures on exposed faces (`face_pressure`), time-history **blast** (`BlastLoad`,
Friedlander waveform; frame heads need the exposed area), **impacts**
(`initial_velocity` with 3 or 6 components). Nodal loads along gravity carry
their **mass** `F/g` (`load_mass=True`, default): otherwise the load would
accelerate the structural mass only and debris would fall at several g.
Self-weight written as a model load (`Model.add_self_weight`) replaces the
internal one and adds no mass (the mass from rho is already there).
`ColumnRemoval(element, t)` removes the element at time `t`; the load of
the nodes it carried stays on the other bodies at the node.

## Integration, damping, contact

Central differences with an automatic stable step (`0.9 * 2 / omega_max`,
largest eigenvalue of the sparse stiffness; a `dt` beyond `2/omega_max` is
rejected). Mass-proportional **damping** (`damping`, xi on `omega_1`) acts
on the motion **relative** to the rigid motion of each group of bodies
connected by intact springs: it damps vibrations but does not brake falling
debris or the rotation about a cracked hinge.

Penalty contacts with a normal dashpot (ratio 0.7) and Coulomb friction:
**ground** `Ground(z=..., friction=...)` on the vertices, with a sticking
tangential spring; **between bodies** (`contact=True`) with a pair normal
from the separating axis (SAT) and contact points on vertices and a 3 x 3
grid on every face (aligned stacked blocks and crossed beams rest
correctly), beams as capsules. The contact stiffness
`contact_stiffness * E * h` is capped so that it never lowers the stable
step: penetration under self-weight stays at micrometres.

## Energy balance

```
W_ext + E_0 = E_cin + E_el + E_diss + E_fract + E_removed
              + E_contact + W_friction + W_contact   (+ residual)
```

`E_fract` is the spring dissipation (fracture, plasticity, crack friction)
measured as the local work of each spring minus the stored energy; the
residual compares this measure with the work on the bodies and is not zero
by construction. In the test cases it stays between 1e-5 and 1e-2, collapses
with ground impacts included.

```python
from feagent import Material, Model, Section
from feagent.aem import AEMModel, ColumnRemoval, Ground, solve_collapse

m = Model()                                  # frame in the XZ plane, fixed bases
# ... nodes, beams, m.fix(...), m.add_nodal_load(n, Fz=-15e3) ...
am = AEMModel.from_model(m, segments_per_beam=6, fy=275e6)
res = solve_collapse(am, scenario=ColumnRemoval(element=3, t=0.8),
                     t_end=3.0, damping=0.05, ramp=0.5,
                     ground=Ground(z=0.0, friction=0.5))
res.energy_balance()                 # balance terms and residual
res.failed_springs()                 # failure timeline
res.end_force(am.source_map[1][-1], 1)   # force at the top of column 1
res.to_vtk("collapse/frame", stride=2)   # ParaView series (.vtu + .pvd)
```

## In the feagent GUI

*Analysis > Progressive collapse (AEM)...* on a model of beams only or
solids only (sheet **Solid**: type, nodes, material; loads in the
SolidBodyForce, SolidFaceLoad, SolidThermal sheets). The dialog asks for the
element to remove and the time, duration, damping, cases, ground and
friction, debris contact and the material parameters (fy, capacity factor,
segments for frames; ft, Gf, fc, crack friction, springs per side for
solids). The result plays with the play button and the time slider: real
positions (scale 1), bodies with broken springs in red, removed bodies
hidden, ground; in the **extruded view** beams are drawn with their section
oriented as the rigid body. *Results* offers the final state, the energies
over time and the failure timeline. Solids are handled by the ordinary
analyses too: boundary faces, deformed shape, von Mises, principal and
component stresses averaged at the nodes.

## Accuracy and validation

Frames: springs lumped at the joints integrate the curvature with the
trapezoidal rule, so the deflection error decreases as 1/n^2:

| case | n = 2 | n = 4 | n = 8 |
|---|---|---|---|
| cantilever, tip load | +12 % | +3.1 % | +0.8 % |
| simply supported, midspan | +13 % | +3.3 % | +0.9 % |
| fixed-fixed, midspan | +50 % | +13 % | +3.7 % |
| portal, load on the beam | +43 % | +11 % | +3.4 % |

Forces governed by equilibrium do not depend on n.

Solids: springs between facing centroids are exact for regular cells;
inclined faces change the stiffness. 2 x 0.25 x 0.25 m cantilever against
the Timoshenko beam: 8 x 1 x 1 hexahedra with a 5 x 5 grid +5 %, each
hexahedron split into 6 pyramids +4 %, split into 2 wedges along the
diagonal -21 %.

`validation/aem/validation_aem.py`: removal of the central column of a 2 x 2
frame (UFC 4-023-03) compared with feagent's FEM solvers (statics of the
intact and damaged frame, Newmark time history of the damaged frame with
the same mass):

| quantity | FEM | AEM | difference |
|---|---|---|---|
| side column N before [kN] | 30.00 | 30.00 | 0.0 % |
| side column N after [kN] | 45.00 | 44.36 | 1.4 % |
| peak N [kN] | 63.58 | 63.71 | 0.2 % |
| final node displacement [mm] | 12.29 | 12.22 | 0.5 % |
| peak displacement [mm] | 24.50 | 25.44 | 3.8 % |

The dynamic amplification factor of the displacement is 2.00 (FEM) and 2.08
(AEM), against the theoretical value 2 for an instantaneous undamped
removal.

## Known limits

* mixed beam + solid models are not supported;
* irregular solid meshes (diagonal wedges, tetrahedra) are less accurate
  than regular hexahedra; small elements lower the stable step (the collapse
  of 16 mixed solids with contact takes about 2 minutes);
* contact between solids detects vertices and face points, not exact edges;
  between beams the capsules have the radius of the equivalent section;
* the axial force at a loaded free end has a 1/(2n) error (half a rigid
  segment beyond the last spring);
* in very violent phases (openings per step of the order of `w_f`) the
  fracture dissipation is underestimated, with a warning.

Examples: `usage_examples/41_aem_progressive_collapse.py` (frames),
`42_aem_solid_fracture.py` (RC cantilever), `43_aem_debris_impact.py`
(stacking, crossed beams, impact, blast), `44_aem_vtk_export.py`.
