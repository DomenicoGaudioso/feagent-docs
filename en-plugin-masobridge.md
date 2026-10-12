---
layout: default
title: "52 - MasoBridge plugin"
parent: English
nav_order: 52
---

# 52 - MasoBridge plugin

The `masobridge` plugin generates and analyses masonry arch bridges with solid elements:
- ring, fill, spandrel walls, parapets, abutments and intermediate piers, with one or more spans;
- linear statics, control sections with the line of thrust;
- nonlinear load-carrying capacity up to collapse;
- nonlinear seismic time-history in one direction.

The computation is **feagent only**, entirely in Python.

## Model

The mesh is made of 8-node hexahedra, conforming, organised in vertical columns: at each abscissa the
levels follow intrados, extrados and road level. Ring, fill, abutments and piers share nodes.

| Group | Material | Notes |
|---|---|---|
| `arco` | masonry | `n_spessore` elements through the thickness |
| `timpano`, `parapetto` | masonry | on the outer strips of the width |
| `riempimento` | fill | `n_riempimento` layers between extrados and road |
| `spalla`, `pila` | masonry | base fixed `profondita_spalla` below the springing |

Literature presets (TRRL tests): `prestwood`, `bolton`, `torksey`, `preston`. They set span, rise,
thickness and fill depth; width and materials must be given.

## Nonlinear laws

| `modello_muratura` | Masonry | Fill | Notes |
|---|---|---|---|
| `danno` (default) | tension/compression damage, rotating cracks that close again | Drucker-Prager | both integrated with IMPL-EX: converges step by step; a step that is not accurate stops the analysis and says so |
| `drucker` | Drucker-Prager with almost no tension | Drucker-Prager | implicit return: with a tensile strength of a few kPa Newton may fail |
| `rankine_cono` | Rankine + friction cone | Drucker-Prager | does not yet reproduce the bare ring: for study only |

**Damage law** (`feagent.solid_nl_danno.DannoTraCo`). The elastic effective
stress is split into positive and negative parts by spectral decomposition, and each part has its own
damage:

*σ = (1 − d⁺) σ̄⁺ + (1 − d⁻) σ̄⁻*

Faria, Oliver and Cervera, IJSS 1998.

- **Tension:** Rankine with threshold *f_t*.
- **Compression:** cone with biaxial strength 1.16 *f_c*.
- **Softening:** exponential, regularised with the fracture energy and the element size of each group, so
  the dissipated energy does not depend on the mesh.
- **Crack closure:** in compression only *d⁻* counts, so an arch hinge works again when the section
  closes.

**IMPL-EX** (Oliver, Huespe and Cante, CMAME 2008):
- within the step, damage and plastic flow of the fill are extrapolated from the previous steps, so the
  law is smooth and Newton converges in a few iterations;
- at the end of the step the state is updated implicitly;
- the step is halved if the implicit stresses are not in equilibrium within 5 % of the loads, and grows
  again when the error is small;
- if a step does not converge even after halving, it is repeated with the stress split frozen to the
  start-of-step directions (linear, hence convergent). `SistemaNL.passi_riserva` counts these steps.

## Actions

| Action | What it does |
|---|---|
| `genera` | builds the mesh with loads and supports |
| `statica` | linear statics with a load patch; stresses in the ring |
| `sezioni` | N, V, M per section and line of thrust, with the middle third |
| `confronto_trave` | solid ring against a curved beam (linear benchmark) |
| `immagini` | PNGs of mesh, stresses and line of thrust |
| `portanza` | self weight under load control, then the patch under displacement control up to collapse; comparison with the limit analysis of the ring |
| `sisma` | NTC/EC8 spectrum-compatible accelerogram at the base, one direction, damage onset times per group |
| `carico_mobile` | envelope of a patch moving along the span (linear) |

The capacity analysis stops at **collapse** only if the load stops growing over a doubled displacement,
with converged steps. A convergence failure stays a convergence failure, and the outcome says so.

## Validation

**Bare ring** (Prestwood geometry, 1 m wide, 0.75 m strip at L/4, *f_t* = 10 kPa). Heyman's static limit
(line of thrust within the thickness, linear programme in `limite.py`) is **15.83 kN**.

| Law | Mesh | Final load | Difference | Time |
|---|---|---|---|---|
| damage | 24 × 3 | 18.7 kN | +18 % | 3 min |
| damage | 48 × 4 | 17.3 kN | +9.5 % | 8 min |
| damage | 24 × 4 | 18.6 kN | +17 % | 2 min |
| Drucker-Prager | 12 × 3 | 19.8 kN, then no convergence at 2.9 mm | +25 % | 29 min |
| Rankine + cone | 12 × 3 | diverges at 1.1 mm | | |

With the damage law the curve converges as the mesh is refined. The remaining excess is the bending lock
of the full hexahedron; incompatible modes do not converge with damage, so use at least 4 elements
through the thickness.

**Material point and bar** (`tests/test_solid_nl_danno.py`):
- tensile dissipated energy equal to *G_f/h* within 4 %;
- peaks at *f_t* and *f_c*;
- exact tangent;
- crack closure;
- a bar that localises the crack in the weak element with peak force *f_t A* and energy *G_f A*.

## Prestwood bridge (TRRL test)

Full-scale test by Page (1987), collapse at **228 kN**, and the 2D reference model of Simoncello et al.
(2020) at 245 kN:
- span 6.55 m, rise 1.428 m, ring 0.22 m thick, width 3.80 m;
- 0.30 m load strip at L/4;
- masonry *f_c* 4.5 MPa, *E* 4130 MPa; *f_t* = 1 kPa, the literature value;
- Mohr-Coulomb fill with φ 30° and *c* 5 kPa.

Not published, hence assumed: fill modulus 200 MPa, zero dilatancy, abutment base fixed 1.5 m below the
springing.

Data in `validation/masobridge/prestwood.py`, with `modello_muratura="danno"` and `ft_muratura=1000`.

| Mesh | Steps | Peak | Stop | Fallback steps | Time |
|---|---|---|---|---|---|
| 16 × 3 | 1000 | 149.2 kN at 1.47 mm | step not accurate at 1.49 mm | 39 | 20 min |
| 24 × 4 | 500 | 134.8 kN at 1.41 mm | step not accurate at the peak | 9 | 13 min |

The analysis reaches the peak, with regular self-weight and loading phases, and stops there: the implicit
state no longer carries the load. This is consistent with the sudden four-hinge collapse of the test, but
following the post-peak branch needs arc-length control (not yet available).

The peak is **35-40 % below the test**. The model is not calibrated:
- fill modulus and strength, fill-spandrel friction and abutment stiffness are unknown;
- the vertical cut at the springing (see the limitations) changes the springing hinge;
- the bending lock of the full hexahedron acts the other way, making the model stronger.

The calibration study is the next step.

With the plastic laws (`drucker`) the same bridge did not get through the self-weight phase with
*f_t* = 1 kPa. With *f_t* = 10 kPa it stopped for lack of convergence between 0.4 and 2.6 mm: see
`validation/masobridge/README.md`.

## Known limitations

- The ring ends with a vertical cut at the springing, not a radial one: at Prestwood the springing section
  is 0.317 m deep instead of 0.22 m. The comparison with Heyman uses the same geometry and is consistent;
  for real bridges it overestimates the springing hinge.
- No interface between ring and fill or between rings: the mesh is conforming.
- Arches with rise above 0.35 times the span: the column hexahedra distort near the springings; the radial
  mesh is not yet available.
- IMPL-EX has a first-order error in the step: results should be confirmed with twice the number of steps.
