---
layout: default
title: "61 - Elements and analyses from the textbooks"
parent: English
nav_order: 61
---

# 61 - Elements and analyses from the textbooks

feagent has been extended by redoing **all the worked examples** of four classic
finite element textbooks; whenever an example needed a missing element or
analysis, the feature was added to the library and to the GUI, verified against
closed-form solutions and then used to redo the example:

* R.D. Cook, D.S. Malkus, M.E. Plesha, *Concepts and Applications of Finite
  Element Analysis*, 3rd ed., Wiley 1989 (page [62](en-62-examples-cook-concepts.html));
* R.D. Cook, *Finite Element Modeling for Stress Analysis*, Wiley 1995
  (page [63](en-63-examples-cook-modeling.html));
* P. Seshu, *Textbook of Finite Element Analysis*, PHI 2003 (page [64](en-64-examples-seshu.html));
* S.S. Bhavikatti, *Finite Element Analysis*, New Age 2005 (page [65](en-65-examples-bhavikatti.html)).

Field problems (heat, torsion, potential flow, seepage) have their own page:
[60](en-60-field-problems.html). This page describes the other new features; the
summary of the example results is at the bottom.

## Plane elements: Q9 and integration rules

`Model.add_plane` accepts the **Lagrangian Q9** (9 nodes: corners, side nodes
(1-2), (2-3), (3-4), (4-1), centre node) and the `integration` option:

| `integration` | rule | use |
|---|---|---|
| `"full"` (default) | full Gauss (T3 1, T6 3, Q4 2x2, Q8/Q9 3x3) | general |
| `"reduced"` | one order less (Q4 1 point, Q8/Q9 2x2, T6 1 point) | removes shear locking; Q4 with `hourglass=True` |
| `"selective"` | reduced shear term `gxy`, full normal terms | bending with Q4 without incompatible modes |
| `"volumetric"` | reduced volumetric part `K m m^T` | nearly incompressible plane strain and axisymmetry |

The one-point Q4 has two zero-energy (*hourglass*) modes. With `hourglass=True`
a stabilisation stiffness is added: the projection of the QM6 stiffness onto the
Flanagan-Belytschko hourglass vectors, which for a rectangle gives the exact
pure-bending energy and does not stiffen linear fields (Cook et al., sec. 6.12).
The cantilever of Cook's Table 18.7-1 (P = 100, L = 2.40, h = 0.40, E = 10⁴,
nu = 0.25) gives:

| mesh | Full book | Full feagent | H-G book | H-G feagent |
|---|---|---|---|---|
| 4 x 2 | 4.562 | 4.564 | 8.572 | 8.573 |
| 8 x 4 | 7.124 | 7.126 | 8.719 | 8.721 |
| 16 x 8 | 8.302 | 8.305 | 8.771 | 8.773 |

## Isoparametric bars

`Model.add_bar(id, nodes, material, A)` creates an axial-only bar with 2 nodes
(`L2`) or 3 nodes (`L3`: ends, then midside node), in any direction, with constant
or **variable** area (one value per node, interpolated: tapered bars). Self-weight
with `add_solid_body_force`, temperature with `add_solid_thermal`, loads along the
axis with `add_bar_line_load` (constant or linear/parabolic with nodal values).
It is the element of the first chapters of Seshu and Bhavikatti: with `L3` the
hanging rod under self-weight is exact with one element.

## Winkler foundation

* `feagent.foundation.add_beam_foundation(model, beam, k_y=, k_z=, k_x=, k_rx=)`:
  consistent stiffness `int N^T diag(k) N dx` with the beam Hermite functions (the
  classic `k L/420 [156 22L 54 -13L ...]`); `foundation_pressure` gives the soil
  reaction along the beam;
* `add_plate_foundation(model, shell, k, lumped=False)`: subgrade modulus under Q4
  shells and triangles (thin DKT triangles recommended: with the Mindlin plate the
  deflection under a point load does not converge).

Both enter as superelements (static, modal, buckling, linear dynamics) and are
saved in the Excel sheets `Foundation` and `PlateFoundation`. Checks: long beam
with a point load `w0 = P beta/(2k)` within 0.01 %, infinite plate
`w0 = P/(8 sqrt(k D))` within 0.3 %.

## Explicit central-difference dynamics

`Model.solve_time_history(..., method="central_difference")` (and
`feagent.dynamics.central_difference_integrate` on matrices) integrates the
equations of motion with the explicit scheme; with diagonal masses nothing is
factorised. The step is checked: if `dt > 2/omega_max` the analysis stops with an
error giving the critical step (Cook et al., sec. 13.10: wave propagation in a bar).

## Large displacements of plane frames

`feagent.corotational.solve_corotational(model, n_steps, control, ...)` analyses
plane frames (X-Y, or X-Z with Z vertical) of Euler-Bernoulli beams, trusses and
bars with the corotational formulation: exact rigid rotations, even beyond 2 pi.
Controls: load, displacement of one DOF, **arc length** (snap-through and snap-back).

![Cantilever](images/books/lib_mensola_anello.png)

*Cantilever with a tip moment increasing up to 2 pi EI/L: the beam closes into a
ring (Cook et al., sec. 17.7); tip displacements within 0.3 % of the exact solution.*

![Von Mises](images/books/lib_von_mises_snap.png)

*Von Mises truss: the arc-length path matches the exact solution also beyond the
limit point.*

## Plasticity, gaps and nonlinear springs

`feagent.inelastic.solve_inelastic(model, steps, plastic=, gaps=, springs=)` is an
incremental Newton-Raphson analysis in which only the listed elements are nonlinear:

* `J2Plasticity(E, sigma_y, nu, H_iso, H_kin)` (or `J2Plasticity.bilinear(E,
  sigma_y, E_t)`): von Mises plasticity with isotropic and kinematic hardening,
  radial return and consistent tangent, for bars, trusses and plane elements (plane
  stress with `s_zz = 0` enforced at the point, plane strain, axisymmetry; in plane
  strain use reduced-integration Q8 to avoid the volumetric locking of plastic flow);
* `Gap(gap, k, side)`: spring reacting after closing a gap (supports with
  clearance, stops);
* `NonlinearElastic(points)`: spring with a piecewise linear force-elongation law.

Non-converging steps are halved; `steps` can be a sequence of factors, also
decreasing (unloading, cycles: Bauschinger effect with kinematic hardening).
`solve_inelastic_explicit` uses central differences (elastic and plastic waves in
the bar of Cook, sec. 17.6).

![Plastic](images/books/lib_mensola_plastica.png)

*Plane-stress cantilever: the load-deflection curve approaches the collapse load
Mp/L (1.03 Mp/L with this mesh, 1.01 on refinement); right, the plastic hinge.*

## Zienkiewicz-Zhu error estimate

`feagent.error_estimation.zz_error(model, result, method="spr")` recovers
continuous nodal stresses (SPR, *superconvergent patch recovery*, or nodal
averaging) and estimates the error in the energy norm element by element; `eta` is
the global relative error and `refinement_ratio` flags the elements to refine.

![ZZ](images/books/lib_zz_mensola.png)

*Estimated error per element in a Q4 cantilever: it concentrates at the clamp.*

## Tests

`tests/test_field.py`, `tests/test_inelastic.py`, `tests/test_corotational.py`,
`tests/test_error_estimation.py`, `tests/test_books_io.py` (Excel sheets) and
`tests/test_books_examples.py` (all the textbook examples).

<!-- riepilogo-libri -->

## Summary of the textbook examples

| Book | Page | Examples | ✅ | 🟡 | 📐 | ⛔ | Checks passed |
|---|---|---|---|---|---|---|---|
| R.D. Cook, *Concepts and Applications of Finite Element Analysis* | [62](en-62-examples-cook-concepts.html) | 135 | 36 | 19 | 77 | 3 | 785/785 |
| R.D. Cook, *Finite Element Modeling for Stress Analysis* | [63](en-63-examples-cook-modeling.html) | 54 | 29 | 13 | 12 | 0 | 452/452 |
| P. Seshu, *Textbook of Finite Element Analysis* | [64](en-64-examples-seshu.html) | 44 | 21 | 2 | 19 | 2 | 422/422 |
| S.S. Bhavikatti, *Finite Element Analysis* | [65](en-65-examples-bhavikatti.html) | 58 | 21 | 2 | 35 | 0 | 361/361 |
