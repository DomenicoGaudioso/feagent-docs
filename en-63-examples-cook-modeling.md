---
layout: default
title: "63 - Examples from Cook (Modeling)"
parent: English
nav_order: 63
---

# 63 - Examples from Cook (Modeling)

This page collects **all the worked examples** of the book *Finite Element Modeling for Stress Analysis* (R.D. Cook, Wiley, 1995) redone with feagent. For each example: reference in the book, feagent model, numerical comparison between the book value and feagent, notes (missing data, book misprints corrected, modelling differences). Texts are summarised in our own words; numerical data are given to redo the computation. The code of every example is in `validation/books/cookmodeling/`; to run them again:

```bash
python -m validation.books.run_books cookmodeling
python scripts/_gen_book_docs.py
```

**Outcome**: ✅ reproduced and compared; 🟡 partly reproduced (book data or results incomplete); 📐 derivation of formulas checked numerically; ⛔ not reproducible (reason in the notes). The *error* column is the largest relative error of the comparisons; *ref.* tells what the comparison is made against: book, exact solution or corrected value of a misprint.

**54 examples**: 29 reproduced, 13 partly, 12 derivations checked, 0 not reproducible; 452 numerical checks, 452 within tolerance.

## Summary

| Example | Title | Outcome | Checks | Max error |
|---|---|---|---|---|
| [Sez. 1.4, Fig. 1.4-1 (Problem 1.9)](#cookmod-1-4-tapered-bar-discretization) | Tapered bar discretized with stepped uniform elements | ✅ | 6/6 | 3.20 % |
| [Sez. 2.5, Figg. 2.5-1d e 2.5-4](#cookmod-2-5-bar-uniform-axial-load) | Bar under uniform axial load: exact nodal displacements, stepped stresses | ✅ | 7/7 | 0.00 % |
| [Sez. 2.6, Example 1, Fig. 2.6-1](#cookmod-2-6-ex1-bar-thermal-free) | Two-element bar heated with one end restrained | ✅ | 4/4 | 0.00 % |
| [Sez. 2.6, Example 2, Eqq. 2.6-7, 2.6-8](#cookmod-2-6-ex2-bar-thermal-fixed) | Two-element bar clamped at both ends and heated | ✅ | 3/3 | 0.00 % |
| [Sez. 2.6, Eqq. 2.6-9, 2.6-10](#cookmod-2-6-spurious-thermal-stress) | Spurious thermal stress in a bar with linear DeltaT | 📐 | 4/4 | 0.00 % |
| [Sez. 2.7 An Application, Figg. 2.7-1...2.7-4](#cookmod-2-7-oval-ring) | Plane oval ring under concentrated forces and internal pressure | ✅ | 14/14 | 0.46 % |
| [Sez. 3.2, Fig. 3.2-2](#cookmod-3-2-cst-pure-bending) | Cantilever in pure bending modelled with constant strain triangles | ✅ | 5/5 | 3.98 % |
| [Sez. 3.6, Eq. 3.6-1](#cookmod-3-6-q4-locking-ratio) | Bending over-stiffness of the rectangular Q4 (locking) | 📐 | 5/5 | 0.00 % |
| [Sez. 3.11, Fig. 3.11-1](#cookmod-3-11-cantilever-element-comparison) | End-loaded cantilever: comparison of CST, Q4, Q6, LST and Q8 | ✅ | 20/20 | 0.17 % |
| [Sez. 3.12 An Application, Tab. 3.12-1](#cookmod-3-12-square-plate-pressurized-hole) | Square plate with a pressurized central hole | 🟡 | 25/25 | 3.99 % |
| [Sez. 4.5, Figg. 4.5-1, 4.5-2, Eqq. 4.5-1...4.5-3](#cookmod-4-5-gauss-quadrature) | Gauss rules with 1, 2 and 3 points in 1D and 2x2, 3x3 in 2D | 📐 | 16/16 | 0.00 % |
| [Sez. 4.6-4.7, Figg. 4.6-1, 4.6-2, 4.7-1](#cookmod-4-6-hourglass-instability) | Spurious (hourglass) modes of the one-point Q4 and of the four-point Q8 | ✅ | 8/8 | 0.00 % |
| [Sez. 4.9, Fig. 4.9-1a](#cookmod-4-9-patch-test) | Patch test for constant sigma_x with 4-node elements | ✅ | 9/9 | 0.00 % |
| [Sez. 4.13, Fig. 4.13-2, Eqq. 4.13-6...4.13-11](#cookmod-4-13-lagrange-penalty-bar) | Constraint u1 = u2 on a two-element bar: Lagrange multipliers and penalty | ✅ | 12/12 | 0.00 % |
| [Sez. 5.5, Fig. 5.5-1, Eqq. 5.5-3...5.5-7](#cookmod-5-5-homogenized-perforated-plate) | Equivalent elastic properties of a perforated plate from a repeating cell | 📐 | 12/12 | 0.39 % |
| [Sez. 5.7, Fig. 5.7-3, Eqq. 5.7-1, 5.7-2](#cookmod-5-7-bolt-pretension-smeared) | Smeared flange bolts and pretension by cooling | 📐 | 4/4 | 0.00 % |
| [Sez. 5.10, Fig. 5.10-1, Eqq. 5.10-1, 5.10-2](#cookmod-5-10-ill-conditioning-springs) | Ill-conditioning: stiff part supported by a flexible part | ✅ | 7/7 | 0.00 % |
| [Sez. 5.10, Fig. 5.10-3](#cookmod-5-10-folded-strip-ill-conditioning) | Arch-like folded strip of flat shells: stress degradation as the thickness decreases | 🟡 | 6/6 | 1.18 % |
| [Sez. 5.14, Fig. 5.14-1](#cookmod-5-14-scf-correction-factor) | Correction factor from a secondary case for the stress near a hole | 🟡 | 5/5 | 4.00 % |
| [Sez. 5.15, Eq. 5.15-1, Fig. 5.15-2](#cookmod-5-15-richardson-extrapolation) | Extrapolation to h -> 0 from two meshes | 📐 | 4/4 | 0.26 % |
| [Sez. 5.16, Figg. 5.16-1, 5.16-2, Eqq. 5.16-1, 5.16-2](#cookmod-5-16-zz-error-adaptive) | Z2 energy error estimator and guided refinement | 🟡 | 8/8 | 0.52 % |
| [Sez. 6.3 A 3D Application, Figg. 6.3-1, 6.3-2, Eq. 6.3-1](#cookmod-6-3-curved-beam-3d) | Curved beam of trapezoidal section in pure bending with Hex8 solids | ✅ | 7/7 | 0.64 % |
| [Sez. 6.5 An Axisymmetric Application, Figg. 6.5-1...6.5-4](#cookmod-6-5-shrink-fit-spinning-disks) | Tapered disk with a shrink-fitted ring, then spinning | 🟡 | 8/8 | 11.86 % |
| [Sez. 7.1, Fig. 7.1-4, Eqq. 7.1-5, 7.1-6](#cookmod-7-1-membrane-string-action) | Beam with immovable hinges: share of load carried by string action | ✅ | 5/5 | 13.39 % |
| [Sez. 7.2 Test Cases, Fig. 7.2-6a](#cookmod-7-2-square-plate-benchmarks) | Simply supported or clamped square plate: centre deflection | ✅ | 6/6 | 0.36 % |
| [Sez. 7.2 Test Cases, Fig. 7.2-6b](#cookmod-7-2-rhombic-plate) | 30-degree rhombic plate, simply supported, uniform load | ✅ | 3/3 | 2.71 % |
| [Sez. 7.2 Test Cases, Fig. 7.2-6c,d](#cookmod-7-2-twisted-strip-plate) | Twisting of a flat cantilever strip | ✅ | 5/5 | 4.28 % |
| [Sez. 7.3 A Plate Application, Figg. 7.3-1...7.3-3](#cookmod-7-3-plate-winkler-point-load) | Plate on a Winkler foundation under a point load | ✅ | 5/5 | 9.01 % |
| [Sez. 7.4, Eq. 7.4-1](#cookmod-7-4-boundary-layer-length) | Width of the bending boundary layer in shells | 📐 | 5/5 | 4.26 % |
| [Sez. 7.5 Test Cases, Fig. 7.5-2](#cookmod-7-5-shell-obstacle-course) | Shell benchmarks: cylindrical roof, pinched cylinder, hemisphere, twisted beam | ✅ | 14/14 | 4.12 % |
| [Sez. 7.5, Fig. 7.5-3](#cookmod-7-5-conical-frusta-sphere) | Pressurized sphere modelled with conical frusta: spurious bending moments | 🟡 | 3/3 | 0.11 % |
| [Sez. 7.6 An Axisymmetric Shell Application, Figg. 7.6-1, 7.6-2](#cookmod-7-6-spherical-segment-axisym-shell) | Spherical zone clamped at the equator with a line load on the upper edge | 🟡 | 7/7 | 5.36 % |
| [Sez. 7.7 A General Shell Application, Figg. 7.7-1...7.7-3](#cookmod-7-7-slit-tube-cantilever) | Thin tube slit lengthwise, cantilevered, under a transverse force | 🟡 | 9/9 | 8.85 % |
| [Sez. 8.2, Fig. 8.2-1, Eq. 8.2-1](#cookmod-8-2-bar-conductivity) | Conductivity matrix of a bar with insulated lateral surface | 📐 | 4/4 | 0.00 % |
| [Sez. 8.4, Eqq. 8.4-1...8.4-5 (Problem 8.11)](#cookmod-8-4-crank-nicolson-scheme) | Time integration of thermal transients with the generalized trapezoidal method | 📐 | 5/5 | 0.55 % |
| [Sez. 8.6 An Application, Figg. 8.6-1...8.6-5, Tab. 8.6-1](#cookmod-8-6-pipe-flange-thermal-stress) | Flange welded to a pipe: steady temperature field and thermal stresses | 🟡 | 20/20 | 24.09 % |
| [Sez. 9.3, Eqq. 9.3-1...9.3-5](#cookmod-9-3-mass-matrices) | Lumped and consistent mass matrices of bar and beam | 📐 | 5/5 | 0.00 % |
| [Sez. 9.4 Example, Fig. 9.4-1, Eqq. 9.4-4...9.4-7](#cookmod-9-4-cantilever-one-element-modal) | Frequencies of a cantilever with a single beam element: consistent and lumped mass | ✅ | 6/6 | 0.18 % |
| [Sez. 9.6 Example, Eqq. 9.6-6...9.6-8](#cookmod-9-6-guyan-one-element) | Guyan reduction of the one-element cantilever | ✅ | 3/3 | 0.01 % |
| [Sez. 9.11, Eqq. 9.11-1, 9.11-2](#cookmod-9-11-frequency-estimates) | First-frequency estimate with static stiffness and effective mass m/2 | ✅ | 4/4 | 0.02 % |
| [Sez. 9.11, Fig. 9.11-4](#cookmod-9-11-guyan-cantilever-plate) | Square cantilever plate: full frequencies and Guyan-reduced with 6 masters | 🟡 | 8/8 | 4.40 % |
| [Sez. 9.11, Fig. 9.11-6, Eqq. 9.11-3...9.11-5](#cookmod-9-11-spin-softening) | Spin softening of a mass-spring system on a rotating disk | 📐 | 3/3 | 0.00 % |
| [Sez. 9.12 An Application, Figg. 9.12-1...9.12-3, Tab. 9.12-1](#cookmod-9-12-frame-modes-harmonic) | Right-angle frame: natural modes and harmonic response | ✅ | 31/31 | 2.37 % |
| [Sez. 9.13 An Application, Figg. 9.13-1...9.13-3, Tabb. 9.13-1, 9.13-2](#cookmod-9-13-frame-impulse-response) | Frame under a rectangular pulse: modal superposition and response spectrum | 🟡 | 40/40 | 6.84 % |
| [Sez. 10.2, Figg. 10.2-1...10.2-3 (Problems 10.2, 10.3)](#cookmod-10-2-nonlinear-spring-algorithms) | Nonlinear spring: incremental, Newton-Raphson and modified Newton-Raphson methods | ✅ | 7/7 | 0.16 % |
| [Sez. 10.3, Fig. 10.3-1a,b, Eq. 10.3-1](#cookmod-10-3-rigid-bar-stress-stiffness) | Rigid bar on a spring with axial force: geometric stiffness P/L | 📐 | 3/3 | 0.00 % |
| [Sez. 10.3, Eqq. 10.3-3...10.3-5 (Problem 10.4)](#cookmod-10-3-one-element-buckling) | Critical load of a cantilever with a single beam element | ✅ | 5/5 | 0.02 % |
| [Sez. 10.4, Fig. 10.4-1, Eqq. 10.4-1...10.4-3 (Problem 10.5)](#cookmod-10-4-beam-on-wall-snap) | Inclined bar with a roller on a wall: displacement-dependent stiffness, limit point and snap-through | ✅ | 8/8 | 0.80 % |
| [Sez. 10.4, Fig. 10.4-2a,b,e](#cookmod-10-4-geometric-nonlinear-tests) | Geometric nonlinearity test cases: rigid bar with rotational spring, cantilever with end moment, membrane | 🟡 | 8/8 | 0.10 % |
| [Sez. 10.5, Figg. 10.5-2, 10.5-3](#cookmod-10-5-plastic-block-sampling) | Plasticity: Gauss points as yield samplers in a bent block | ✅ | 9/9 | 0.21 % |
| [Sez. 10.6, Figg. 10.6-2, 10.6-3 (Problem 10.12)](#cookmod-10-6-gap-two-springs) | Two springs in series with a gap g to a wall: penalty gap element | ✅ | 4/4 | 0.00 % |
| [Sez. 10.8 Applications, Figg. 10.8-1, 10.8-2](#cookmod-10-8-frame-geometric-nonlinearity) | Slender frame: linear buckling and large-displacement response with snap | ✅ | 7/7 | 0.46 % |
| [Sez. 10.8 Applications, 'Plastic Action in a Beam', Figg. 10.8-3, 10.8-4](#cookmod-10-8-plastic-beam-plane-stress) | Plane-stress beam with bilinear plasticity: loading, unloading and residual stresses | 🟡 | 6/6 | 6.23 % |
| [Sez. 10.8 Applications, 'Beam With Contact Nonlinearity', Fig. 10.8-5](#cookmod-10-8-beam-gap-contact) | Beam with an initial gap at an intermediate support and lift-off at the end | ✅ | 5/5 | 0.27 % |

## Chapter 1

<a id="cookmod-1-4-tapered-bar-discretization"></a>

### Sez. 1.4, Fig. 1.4-1 (Problem 1.9) - Tapered bar discretized with stepped uniform elements

✅ · PDF page 19 · bar

A variable-section bar loaded by an end axial force is replaced by stepped uniform elements whose area is taken at each element centre. The book discusses the discretization error, which decreases as the mesh is refined, without printing numbers. Data chosen as in Problem 1.9: rectangular section of constant thickness t = 1, depth from h0 = 10 to h1 = 30, L = 100, E = 200000, P = 1000.

**Model**: Constant-area L2 bars (area taken at the element centre) with 4, 8, 16 and 64 elements; for comparison L2 bars with linearly interpolated nodal areas.

![cookmod-1.4-tapered-bar-discretization](images/books/cookmod-1.4-tapered-bar-discretization.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| displacement, 4 steps (exact sum over the steps) | exact | 0.027244 | 0.027244 | 0.00 % | ✅ |
| displacement, 8 steps (exact sum over the steps) | exact | 0.027408 | 0.027408 | 0.00 % | ✅ |
| displacement, 16 steps (exact sum over the steps) | exact | 0.027451 | 0.027451 | 0.00 % | ✅ |
| ratio of the errors with 4 and 8 steps (h^2 convergence) | exact | 4 | 3.8721 | 3.20 % | ✅ |
| displacement, 64 steps | exact | 0.027465 | 0.027464 | 0.00 % | ✅ |
| 16 linearly tapered bars versus 16 steps (stiffness E A_mean / L) | other | 0.027451 | 0.027451 | 0.00 % | ✅ |

**Notes**: The book prints no numerical results: the reference is the exact solution delta = P L ln(h1/h0) / (E t (h1 - h0)). With 4 steps the error is about -0.8 % (the stepped bar is stiffer, because the area at the centre of each step exceeds the harmonic mean) and it decreases with the square of the step size (ratio 3.9 between 4 and 8 steps). The L2 bar with linear area gives the same stiffness E A_mean / L as the steps, as the book's Problem 2.1 asks to show (the standard formulation differs from the exact stiffness of the tapered segment).

*Code*: `validation/books/cookmodeling/ch01.py`

## Chapter 2

<a id="cookmod-2-5-bar-uniform-axial-load"></a>

### Sez. 2.5, Figg. 2.5-1d e 2.5-4 - Bar under uniform axial load: exact nodal displacements, stepped stresses

✅ · PDF page 38 · bar

Bar clamped at one end under a uniform axial load q, divided into three equal elements. The book shows graphically that the nodal displacements are exact, the stresses are piecewise constant, the element-centre value and the nodal average are the most accurate and the peak stress at the clamp is underestimated. Data chosen: q = 1, L = 1000, A = 100, E = 200000.

**Model**: 3 L2 bars of length L, uniform axial load q with consistent nodal forces (add_bar_line_load), end x = 0 clamped.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| displacement of node 2 [mm] | exact | 0.125 | 0.125 | 0.00 % | ✅ |
| displacement of node 3 [mm] | exact | 0.2 | 0.2 | 0.00 % | ✅ |
| displacement of node 4 [mm] | exact | 0.225 | 0.225 | 0.00 % | ✅ |
| stress at the centre of element 1 [MPa] | exact | 25 | 25 | 0.00 % | ✅ |
| stress at the centre of element 2 [MPa] | exact | 15 | 15 | 0.00 % | ✅ |
| stress at the centre of element 3 [MPa] | exact | 5 | 5 | 0.00 % | ✅ |
| stress at the clamp, FE / exact ratio (5/6) | exact | 0.83333 | 0.83333 | 0.00 % | ✅ |

**Notes**: The book prints no numbers: the references are the exact solutions u = q (3 L x - x^2 / 2) / (E A) and sigma = q (3 L - x) / A.

*Code*: `validation/books/cookmodeling/ch02.py`

<a id="cookmod-2-6-ex1-bar-thermal-free"></a>

### Sez. 2.6, Example 1, Fig. 2.6-1 - Two-element bar heated with one end restrained

✅ · PDF page 40 · bar, thermal_stress

Two-element bar restrained at one end only, uniformly heated by DeltaT and loaded by two opposite forces P. The book solves it symbolically: u2 = alpha L DeltaT, u3 = 2 alpha L DeltaT + P L / (A E), zero stress in the first element and P / A in the second. Values chosen: E = 200000 MPa, A = 100 mm2, L = 1000 mm, alpha = 12e-6, DeltaT = 50, P = 1000 N.

**Model**: 2 equal L2 bars, uniform temperature change (add_solid_thermal), u1 = 0, forces -P at node 2 and +P at node 3.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u2 = alpha L DeltaT [mm] | book | 0.6 | 0.6 | 0.00 % | ✅ |
| u3 = 2 alpha L DeltaT + P L / (A E) [mm] | book | 1.25 | 1.25 | 0.00 % | ✅ |
| stress in element 1 (zero) [MPa] | book | 0 | -2.1684e-14 | 0.00 % | ✅ |
| stress in element 2 = P / A [MPa] | book | 10 | 10 | 0.00 % | ✅ |

**Notes**: References: the book's symbolic formulas (Eqs. 2.6-4 and 2.6-6) evaluated with the chosen data.

*Code*: `validation/books/cookmodeling/ch02.py`

<a id="cookmod-2-6-ex2-bar-thermal-fixed"></a>

### Sez. 2.6, Example 2, Eqq. 2.6-7, 2.6-8 - Two-element bar clamped at both ends and heated

✅ · PDF page 42 · bar, thermal_stress

The bar of the previous example is clamped at both ends: u2 = -P L / (2 A E), sigma1 = -P / (2A) - E alpha DeltaT, sigma2 = +P / (2A) - E alpha DeltaT. Same numerical data as Example 1.

**Model**: 2 L2 bars, u1 = u3 = 0, force -P at node 2, uniform DeltaT.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u2 = -P L / (2 A E) [mm] | book | -0.025 | -0.025 | 0.00 % | ✅ |
| sigma1 = -P/(2A) - E alpha DeltaT [MPa] | book | -125 | -125 | 0.00 % | ✅ |
| sigma2 = +P/(2A) - E alpha DeltaT [MPa] | book | -115 | -115 | 0.00 % | ✅ |

**Notes**: The signs of Eqs. 2.6-8 are unreadable in the OCR and were rebuilt from Eq. 2.6-5: sigma1 = E u2 / L - E alpha DeltaT, sigma2 = -E u2 / L - E alpha DeltaT. The book remarks that the results are exact because the required field is representable by the elements.

*Code*: `validation/books/cookmodeling/ch02.py`

<a id="cookmod-2-6-spurious-thermal-stress"></a>

### Sez. 2.6, Eqq. 2.6-9, 2.6-10 - Spurious thermal stress in a bar with linear DeltaT

📐 · PDF page 42 · bar, thermal_stress

A free bar with DeltaT = c x must stay stress-free, but with the constant-strain element the computed stress is sigma = E alpha c (L/2 - x): spurious, zero only at the centre. u2 = alpha c L^2 / 2. Values chosen: c = 0.05 degrees/mm, L = 1000 mm.

**Model**: 1 L2 bar restrained at node 1, nodal temperatures 0 and c L interpolated linearly (add_solid_thermal with nodal values).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u2 = alpha c L^2 / 2 [mm] | book | 0.3 | 0.3 | 0.00 % | ✅ |
| spurious stress at node 1 = E alpha c L / 2 [MPa] | book | 60 | 60 | 0.00 % | ✅ |
| spurious stress at node 2 = -E alpha c L / 2 [MPa] | book | -60 | -60 | 0.00 % | ✅ |
| stress at the centre (correct, zero) [MPa] | book | 0 | 0 | 0.00 % | ✅ |

**Notes**: feagent reproduces the book's formula: nodal stresses +-E alpha c L / 2 and zero stress at the centre, where the book recommends evaluating DeltaT.

*Code*: `validation/books/cookmodeling/ch02.py`

<a id="cookmod-2-7-oval-ring"></a>

### Sez. 2.7 An Application, Figg. 2.7-1...2.7-4 - Plane oval ring under concentrated forces and internal pressure

✅ · PDF page 43 · beam_2d

Flat oval ring (two semicircles of mean radius 40 mm joined by 120 mm straight parts, 9 x 5 mm section, steel E = 200 GPa) loaded by two opposite inward forces F = 300 N and by 1 MPa pressure on the central straight parts. The book compares an equivalent circular ring with two beam meshes of the quadrant: displacements of A and D, axial and bending stresses at A and D.

**Model**: Euler-Bernoulli beams in the X-Y plane on one quadrant: coarse mesh with one chord on arc AB at 45 degrees plus BC and CD; fine mesh (Fig. 2.7-4c) with two chords on the arc and CD split into two elements. Symmetry: v and rotation fixed at A, u and rotation fixed at D. Work-equivalent nodal loads f = p t b / 2 at C and D and moment m = p t b^2 / 12 at C; F/2 = 150 N at A.

![cookmod-2.7-oval-ring](images/books/cookmod-2.7-oval-ring.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| uA, coarse mesh [mm] | book | 0.135 | 0.1353 | 0.22 % | ✅ |
| vD, coarse mesh [mm] | book | 0.316 | 0.316 | 0.00 % | ✅ |
| axial stress at A, coarse mesh [MPa] | book | 0 | -4.0422e-14 | 0.00 % | ✅ |
| axial stress at D, coarse mesh [MPa] | book | -3.33 | -3.3333 | 0.10 % | ✅ |
| bending stress at A, coarse mesh [MPa] | book | 166 | 166.1 | 0.06 % | ✅ |
| bending stress at D, coarse mesh [MPa] | book | 117 | 117.24 | 0.20 % | ✅ |
| uA, fine mesh [mm] | book | 0.121 | 0.12156 | 0.46 % | ✅ |
| vD, fine mesh [mm] | book | 0.349 | 0.35003 | 0.30 % | ✅ |
| axial stress at A, fine mesh [MPa] | book | 1.8 | 1.804 | 0.22 % | ✅ |
| axial stress at D, fine mesh [MPa] | book | -3.33 | -3.3333 | 0.10 % | ✅ |
| bending stress at A, fine mesh [MPa] | book | 163 | 163.13 | 0.08 % | ✅ |
| bending stress at D, fine mesh [MPa] | book | 116 | 116.04 | 0.03 % | ✅ |
| radius of the equivalent ring [mm] | book | 78.2 | 78.197 | 0.00 % | ✅ |
| equivalent ring: radial displacement 0.143 F r^3 / (E I) [mm] | book | 0.338 | 0.33766 | 0.10 % | ✅ |

**Notes**: The two meshes of the book are reproduced. In the fine mesh the loaded part CD is also split in two (three load arrows in Fig. 2.7-4c): with CD whole the bending stress at D would be 120 MPa instead of 116, because the end moment computed as k d lacks the fixed-end moment of the distributed load. The axial stress at A of the coarse mesh is zero because the 45-degree member AB carries only shear (the book's explanation). With 16 chords on the arc (converged mesh) uA = 0.121 mm and vD = 0.365 mm: the book itself suggests a further refinement. Shear deformation is neglected, as in the book (small h/R).

*Code*: `validation/books/cookmodeling/ch02.py`

## Chapter 3

<a id="cookmod-3-2-cst-pure-bending"></a>

### Sez. 3.2, Fig. 3.2-2 - Cantilever in pure bending modelled with constant strain triangles

✅ · PDF page 57 · plane_stress

A beam 5a long and a deep, modelled with a single row of CSTs, is loaded in pure bending. The book notes that sigma_x on the neutral axis is a square wave instead of zero, that deflections and stresses are about one quarter of the correct ones and that LSTs reproduce pure bending exactly. Data chosen: a = 1, P = 1, E = 1000, nu = 0.25.

**Model**: 10 T3 triangles (5 a x a squares split by the diagonal), a single row; same mesh with T6 for comparison. Upper clamp node on a roller, lower node pinned; force couple P at the free end.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| tip deflection, CST / exact (about 1/4) | book | 0.25 | 0.24004 | 3.98 % | ✅ |
| sigma_x of the first CST / exact extreme-fibre value (about 1/4) | book | 0.25 | 0.25717 | 2.87 % | ✅ |
| sigma_x of the two triangles of a square: equal and opposite (square wave) | other | -1 | -1 | 0.00 % | ✅ |
| tip deflection with T6 (exact) | exact | 0.15 | 0.15 | 0.00 % | ✅ |
| extreme-fibre sigma_x with T6 (exact, 6 P / a) | exact | 6 | 6 | 0.00 % | ✅ |

**Notes**: The book gives no numbers, only the ratio of about 1/4: feagent gives 0.240 for the deflection and 0.257 for the stress (constant value of the first triangle against the exact value at the extreme fibre, 6 P / a). The comparison with 1/4 is therefore made with a 10 % tolerance. sigma_x changes sign from one triangle to the next above and below the axis (square wave). With T6 elements deflection and stresses are exact.

*Code*: `validation/books/cookmodeling/ch03.py`

<a id="cookmod-3-6-q4-locking-ratio"></a>

### Sez. 3.6, Eq. 3.6-1 - Bending over-stiffness of the rectangular Q4 (locking)

📐 · PDF page 62 · plane_stress

The book derives the ratio between the moment M2 the Q4 needs to rotate its vertical sides by theta and the moment M1 of the exact block: M2/M1 = [1/(1-nu) + (a/b)^2/2] / (1+nu), which grows without bound with a/b (locking). The remedy is the Q6 with incompatible modes.

**Model**: One rectangular 2a x 2b PlaneQ4 (library stiffness()) with its vertical sides rotated by theta; same element with incompatible modes (QM6).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Q4: M2/M1 with a/b = 1, nu = 0.0 | book | 1.5 | 1.5 | 0.00 % | ✅ |
| Q4: M2/M1 with a/b = 2, nu = 0.3 | book | 2.6374 | 2.6374 | 0.00 % | ✅ |
| Q4: M2/M1 with a/b = 5, nu = 0.25 | book | 11.067 | 11.067 | 0.00 % | ✅ |
| Q4: M2/M1 with a/b = 0.5, nu = 0.3 | book | 1.1951 | 1.1951 | 0.00 % | ✅ |
| QM6: M2/M1 with a/b = 5 (no locking) | exact | 1 | 1 | 0.00 % | ✅ |

**Notes**: Book formula verified on the stiffness matrix of the feagent Q4 for four combinations of a/b and nu. The feagent QM6 gives M2/M1 = 1 (exact pure bending on the rectangle).

*Code*: `validation/books/cookmodeling/ch03.py`

<a id="cookmod-3-11-cantilever-element-comparison"></a>

### Sez. 3.11, Fig. 3.11-1 - End-loaded cantilever: comparison of CST, Q4, Q6, LST and Q8

✅ · PDF page 73 · plane_stress, beam_2d

Cantilever L = 10, depth 2, thickness 1, E = 1e4, nu = 0.3, tip force P = 20. Beam theory gives v_C = 1.031 (with shear) and sigma_xB = 300 at the bottom fibre of the clamp. The book compares deflection and stress of ten models.

**Model**: 10 x 2 cantilever in plane stress: one Timoshenko beam (shear area 5/6 A), 10 T3, 5 Q4, 5 QM6 (Q4 incompatible=True), 4 T6, 2 Q8 with 2x2 reduced integration, in regular and distorted meshes. Supports and loads of Fig. 3.11-1b (nodal forces at the free end, clamp reactions spread by forces applied at the roller nodes). Quadrilateral stresses extrapolated to B from the 2x2 Gauss points.

![cookmod-3.11-cantilever-element-comparison](images/books/cookmod-3.11-cantilever-element-comparison.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| sigma_xB, 2-node beam | book | 300 | 300 | 0.00 % | ✅ |
| v_C, 2-node beam | book | 1.031 | 1.0312 | 0.02 % | ✅ |
| sigma_xB, CST | book | 71 | 71.017 | 0.02 % | ✅ |
| v_C, CST | book | 0.264 | 0.26381 | 0.07 % | ✅ |
| sigma_xB, Q4 | book | 200 | 200 | 0.00 % | ✅ |
| v_C, Q4 | book | 0.693 | 0.69333 | 0.05 % | ✅ |
| sigma_xB, Q4 distorted | book | 190 | 190.33 | 0.17 % | ✅ |
| v_C, Q4 distorted | book | 0.502 | 0.5018 | 0.04 % | ✅ |
| sigma_xB, Q6 | book | 270 | 270 | 0.00 % | ✅ |
| v_C, Q6 | book | 1.016 | 1.016 | 0.00 % | ✅ |
| sigma_xB, Q6 distorted | book | 281 | 280.67 | 0.12 % | ✅ |
| v_C, Q6 distorted | book | 0.98 | 0.9799 | 0.01 % | ✅ |
| sigma_xB, LST | book | 254 | 253.84 | 0.06 % | ✅ |
| v_C, LST | book | 0.987 | 0.98745 | 0.05 % | ✅ |
| sigma_xB, LST distorted | book | 250 | 250.32 | 0.13 % | ✅ |
| v_C, LST distorted | book | 0.924 | 0.92425 | 0.03 % | ✅ |
| sigma_xB, Q8 | book | 300 | 300 | 0.00 % | ✅ |
| v_C, Q8 | book | 1.028 | 1.0281 | 0.01 % | ✅ |
| sigma_xB, Q8 distorted | book | 278 | 277.52 | 0.17 % | ✅ |
| v_C, Q8 distorted | book | 1.035 | 1.035 | 0.00 % | ✅ |

**Notes**: Coordinates of the distorted meshes read from the figure (bottom nodes x = 0, 1, 2, 4, 7, 10 and top nodes x = 0, 2, 4, 5, 6, 10; inner side of Q8 and T6 from (3, -1) to (7, 1)). All twenty values match the book within rounding provided that: the book's Q6 is the QM6 (incompatible modes with Taylor's correction), the Q8 is integrated with 2x2 points (with 3x3 the regular mesh gives v_C = 1.007 and sigma_xB = 275) and the quadrilateral stress at B is extrapolated from the Gauss points (in the distorted meshes the value evaluated directly at the node would be 256 for Q4, 373 for Q6, 359 for Q8).

*Code*: `validation/books/cookmodeling/ch03.py`

<a id="cookmod-3-12-square-plate-pressurized-hole"></a>

### Sez. 3.12 An Application, Tab. 3.12-1 - Square plate with a pressurized central hole

🟡 · PDF page 73 · plane_stress

Square plate 20 x 20 mm, 1 mm thick, with a central 7 mm radius hole under pressure p = 1 MPa (E = 1e7 MPa as printed, nu = 0.3). The book compares displacements and sigma1 at points A...F with a coarse symmetric mesh and a finer unsymmetric one, and concludes that the maximum sigma1 is about 3.1 MPa, at A and C or at E (experiments: 2.9 MPa at B and D, 2.7 MPa at E).

**Model**: QM6 quadrilaterals (Q4 with incompatible modes) on one quadrant in plane stress: coarse 9-element mesh rebuilt from Fig. 3.12-2a (arc nodes at 0, 22.5, 45, 67.5 and 90 degrees) and a 24 x 12 mapped mesh graded towards the hole as a converged reference; pressure on the arc sides (add_solid_pressure), u = 0 on AB and v = 0 on CD. Maximum principal stress from nodal averages.

![cookmod-3.12-square-plate-pressurized-hole](images/books/cookmod-3.12-square-plate-pressurized-hole.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| 10^6 v at A, coarse mesh [mm] | book | 2.08 | 2.0893 | 0.45 % | ✅ |
| sigma1 at A, coarse mesh [MPa] | book | 2.11 | 2.0709 | 1.85 % | ✅ |
| 10^6 v at B, coarse mesh [mm] | book | 1.78 | 1.7845 | 0.25 % | ✅ |
| sigma1 at B, coarse mesh [MPa] | book | 2.28 | 2.271 | 0.39 % | ✅ |
| 10^6 u at C, coarse mesh [mm] | book | 2.08 | 2.0893 | 0.45 % | ✅ |
| sigma1 at C, coarse mesh [MPa] | book | 2.11 | 2.0709 | 1.85 % | ✅ |
| 10^6 u at D, coarse mesh [mm] | book | 1.78 | 1.7845 | 0.25 % | ✅ |
| sigma1 at D, coarse mesh [MPa] | book | 2.28 | 2.271 | 0.39 % | ✅ |
| 10^6 u at E, coarse mesh [mm] | book | 1.22 | 1.2079 | 0.99 % | ✅ |
| 10^6 v at E, coarse mesh [mm] | book | 1.22 | 1.2079 | 0.99 % | ✅ |
| sigma1 at E, coarse mesh [MPa] | book | 2.68 | 2.6675 | 0.47 % | ✅ |
| 10^6 u at F, coarse mesh [mm] | book | 0.97 | 0.96901 | 0.10 % | ✅ |
| 10^6 v at F, coarse mesh [mm] | book | 0.97 | 0.96901 | 0.10 % | ✅ |
| sigma1 at F, coarse mesh [MPa] | book | 0.38 | 0.36485 | 3.99 % | ✅ |
| 10^6 displacement at A, mapped mesh versus the book's finer mesh [mm] | book | 2.28 | 2.3344 | 2.39 % | ✅ |
| sigma1 at A, mapped mesh versus the book's finer mesh [MPa] | book | 1.92 | 1.8523 | 3.53 % | ✅ |
| 10^6 displacement at B, mapped mesh versus the book's finer mesh [mm] | book | 1.96 | 2.0098 | 2.54 % | ✅ |
| sigma1 at B, mapped mesh versus the book's finer mesh [MPa] | book | 3.01 | 3.0718 | 2.05 % | ✅ |
| 10^6 displacement at C, mapped mesh versus the book's finer mesh [mm] | book | 2.34 | 2.3344 | 0.24 % | ✅ |
| sigma1 at C, mapped mesh versus the book's finer mesh [MPa] | book | 1.84 | 1.8523 | 0.67 % | ✅ |
| 10^6 displacement at D, mapped mesh versus the book's finer mesh [mm] | book | 2.01 | 2.0098 | 0.01 % | ✅ |
| sigma1 at D, mapped mesh versus the book's finer mesh [MPa] | book | 3.12 | 3.0718 | 1.55 % | ✅ |
| 10^6 displacement at E, mapped mesh versus the book's finer mesh [mm] | book | 1.27 | 1.2597 | 0.81 % | ✅ |
| sigma1 at E, mapped mesh versus the book's finer mesh [MPa] | book | 3.16 | 3.2307 | 2.24 % | ✅ |
| mean ligament stress F / ((a - r) t) [MPa] | book | 2.3 | 2.3333 | 1.45 % | ✅ |

**Notes**: The book's meshes are only drawn. The rebuilt coarse mesh (coordinates read from the figure, symmetrized about the diagonal) reproduces Table 3.12-1 within 2 %; the unsymmetric finer mesh cannot be rebuilt and is replaced by a converged mapped mesh, compared with the book's finer-mesh values with a 5 % tolerance (the book's finer mesh is not yet converged: the book itself calls for another refinement). The converged mesh (stable when the elements are doubled and when Q8 are used) gives sigma1 = 3.10 MPa at B and D and 3.23 MPa at E, which is the maximum; at A and C sigma1 = 1.85 MPa. The book's text places the maximum 'at A and C or at E', while its own table places it at B, D and E: probably a slip for B and D. The book's energy error eta (0.373 and 0.183) needs the ZZ estimator, not yet available.

*Code*: `validation/books/cookmodeling/ch03.py`

## Chapter 4

<a id="cookmod-4-5-gauss-quadrature"></a>

### Sez. 4.5, Figg. 4.5-1, 4.5-2, Eqq. 4.5-1...4.5-3 - Gauss rules with 1, 2 and 3 points in 1D and 2x2, 3x3 in 2D

📐 · PDF page 92 · numerics

The book lists points and weights of the Gauss rules of order 1, 2 and 3 (xi = 0, W = 2; xi = +-1/sqrt(3), W = 1; xi = 0 with W = 8/9 and xi = +-sqrt(0.6) with W = 5/9), the weights of the 3x3 rule on the square (25/81, 40/81, 64/81) and the property that n points integrate polynomials of degree 2n - 1 exactly.

**Model**: Gauss-Legendre rules used by feagent for the elements (feagent.solid._gl and feagent.plane._quad_rule).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| n=1 W | book | 2 | 2 | 0.00 % | ✅ |
| n=1 xi | book | 0 | 0 | 0.00 % | ✅ |
| n=2 xi | book | 0.57735 | 0.57735 | 0.00 % | ✅ |
| n=2 W | book | 1 | 1 | 0.00 % | ✅ |
| n=3 xi | book | 0.7746 | 0.7746 | 0.00 % | ✅ |
| n=3 W0 | book | 0.88889 | 0.88889 | 0.00 % | ✅ |
| n=3 W1 | book | 0.55556 | 0.55556 | 0.00 % | ✅ |
| 3x3 rule: weight of the points (corners) | book | 0.30864 | 0.30864 | 0.00 % | ✅ |
| 3x3 rule: weight of the points (sides) | book | 0.49383 | 0.49383 | 0.00 % | ✅ |
| 3x3 rule: weight of the points (centre) | book | 0.79012 | 0.79012 | 0.00 % | ✅ |
| 1 points: integral of xi^0 + xi^1 (exact) | exact | 2 | 2 | 0.00 % | ✅ |
| 1 points: xi^2 not integrated exactly (1 = true) | exact | 1 | 1 | 0.00 % | ✅ |
| 2 points: integral of xi^2 + xi^3 (exact) | exact | 0.66667 | 0.66667 | 0.00 % | ✅ |
| 2 points: xi^4 not integrated exactly (1 = true) | exact | 1 | 1 | 0.00 % | ✅ |
| 3 points: integral of xi^4 + xi^5 (exact) | exact | 0.4 | 0.4 | 0.00 % | ✅ |
| 3 points: xi^6 not integrated exactly (1 = true) | exact | 1 | 1 | 0.00 % | ✅ |

**Notes**: Points, weights and exactness on monomials of degree 2n - 1 are verified (and the loss of exactness at degree 2n).

*Code*: `validation/books/cookmodeling/ch04.py`

<a id="cookmod-4-6-hourglass-instability"></a>

### Sez. 4.6-4.7, Figg. 4.6-1, 4.6-2, 4.7-1 - Spurious (hourglass) modes of the one-point Q4 and of the four-point Q8

✅ · PDF page 94 · plane_stress, numerics

With too few Gauss points some deformation modes produce no strain at the integration points: the 2 x 2 mesh of one-point Q4 has three instabilities (u = cxy, v = -cxy and the mixed mode), a cantilever of one-point Q4 may show huge displacements, the four-point Q8 has an hourglass mode u = x (1 - 3 y^2), v = y (3 x^2 - 1) that does not propagate between adjacent elements.

**Model**: Q4 with integration='reduced' (1 point) in a 2 x 2 mesh and in a clamped 8 x 1 cantilever; Q8 with integration='reduced' (2 x 2 points), single and in pairs; for comparison the 2 x 2 point Q4 and the stabilized reduced Q4 (hourglass=True). Zero-energy modes counted from the eigenvalues of the assembled stiffness.

![cookmod-4.6-hourglass-instability](images/books/cookmod-4.6-hourglass-instability.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| zero-energy modes, 2 x 2 mesh of 1-point Q4 (3 rigid + 3 spurious) | book | 6 | 6 | 0.00 % | ✅ |
| zero-energy modes, 2 x 2 mesh of 2 x 2-point Q4 (rigid only) | book | 3 | 3 | 0.00 % | ✅ |
| zero-energy modes, stabilized 1-point Q4 | book | 3 | 3 | 0.00 % | ✅ |
| zero-energy modes, one 2 x 2-point Q8 (3 rigid + 1 hourglass) | book | 4 | 4 | 0.00 % | ✅ |
| energy of the mode u = x(1 - 3y^2), v = y(3x^2 - 1) in the reduced Q8 | book | 0 | 3.9690e-15 | 0.00 % | ✅ |
| zero-energy modes, two 2 x 2-point Q8 (non-communicable mode) | book | 3 | 3 | 0.00 % | ✅ |
| 1-point Q4 cantilever: load displacement larger than the length (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| resultant of the Gauss-point sigma_x over a section (= P) | exact | 1 | 1 | 0.00 % | ✅ |

**Notes**: The catalogue marked this case as not reproducible: feagent now exposes reduced integration for plane elements. The counts of zero-energy modes match the book's description (3 rigid motions plus 3 instabilities in the 2 x 2 mesh; 3 plus 1 in the single Q8; no instability with two Q8). In the 8 x 2 cantilever of one-point Q4, with a unit axial force at the corner node, the load displacement is 368 against 15.3 for the 2 x 2-point Q4: more than the bar length, as the book warns (it gives no numbers); the Gauss-point stresses instead stay in equilibrium with the load (Fig. 4.7-1). With a single row of elements the mesh is truly a mechanism and feagent refuses the analysis.

*Code*: `validation/books/cookmodeling/ch04.py`

<a id="cookmod-4-9-patch-test"></a>

### Sez. 4.9, Fig. 4.9-1a - Patch test for constant sigma_x with 4-node elements

✅ · PDF page 99 · plane_stress

A patch of elements with at least one interior node, loaded by forces F, 2F, F on vertical sides 2H high, must give sigma_x = 2F / (H t) constant at every point and the other stresses zero. The book's figure is qualitative.

**Model**: Patch of four distorted quadrilaterals around an off-centre interior node (chosen coordinates), with Q4, QM6 and Q8 (the latter with midside nodes); nodal forces work-equivalent to uniform tension on the vertical sides, minimal supports against rigid motions.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Q4: minimum sigma_x at the Gauss points | exact | 2 | 2 | 0.00 % | ✅ |
| Q4: maximum sigma_x at the Gauss points | exact | 2 | 2 | 0.00 % | ✅ |
| Q4: maximum of |sigma_y| and |tau_xy| (zero) | exact | 0 | 1.0008e-15 | 0.00 % | ✅ |
| QM6: minimum sigma_x at the Gauss points | exact | 2 | 2 | 0.00 % | ✅ |
| QM6: maximum sigma_x at the Gauss points | exact | 2 | 2 | 0.00 % | ✅ |
| QM6: maximum of |sigma_y| and |tau_xy| (zero) | exact | 0 | 2.4425e-15 | 0.00 % | ✅ |
| Q8: minimum sigma_x at the Gauss points | exact | 2 | 2 | 0.00 % | ✅ |
| Q8: maximum sigma_x at the Gauss points | exact | 2 | 2 | 0.00 % | ✅ |
| Q8: maximum of |sigma_y| and |tau_xy| (zero) | exact | 0 | 6.9944e-15 | 0.00 % | ✅ |

**Notes**: Node coordinates chosen (distorted patch, interior node at (0.9, 1.15)); the nodes of the vertical sides are not evenly spaced, so the nodal forces are the work-equivalent ones of each side and not exactly F, 2F, F. All three elements pass the test: the feagent QM6 passes it thanks to Taylor's correction of the incompatible modes.

*Code*: `validation/books/cookmodeling/ch04.py`

<a id="cookmod-4-13-lagrange-penalty-bar"></a>

### Sez. 4.13, Fig. 4.13-2, Eqq. 4.13-6...4.13-11 - Constraint u1 = u2 on a two-element bar: Lagrange multipliers and penalty

✅ · PDF page 107 · bar, constraints

Bar of two equal elements with the constraint u1 = u2. With Lagrange multipliers u1 = u2 = P/k and lambda = P (constraint force); with the penalty u1 = (2 + alpha) P / ((1 + alpha) k), u2 = P/k, which tends to the free solution (u1 = 2P/k) for alpha = 0 and to the constrained one for large alpha. Data chosen: k = 1, P = 1.

**Model**: Two L2 bars (k = AE/L = 1) with node 3 clamped and force P at node 1. Exact constraint with add_equal_dof; Lagrange multiplier with the bordered system built on the stiffness assembled by feagent; penalty as a spring between nodes 1 and 2 of stiffness alpha k (= C^T alpha C with C = [sqrt(k), -sqrt(k)]).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| exact constraint: u1 = P/k | book | 1 | 1 | 0.00 % | ✅ |
| exact constraint: u2 = P/k | book | 1 | 1 | 0.00 % | ✅ |
| feagent reduced K: k22 = 2k | book | 2 | 2 | 0.00 % | ✅ |
| |lambda| = P (constraint force) | book | 1 | 1 | 0.00 % | ✅ |
| penalty alpha = 0: u1 = (2+alpha)P/((1+alpha)k) | book | 2 | 2 | 0.00 % | ✅ |
| penalty alpha = 0: u2 = P/k | book | 1 | 1 | 0.00 % | ✅ |
| penalty alpha = 1: u1 = (2+alpha)P/((1+alpha)k) | book | 1.5 | 1.5 | 0.00 % | ✅ |
| penalty alpha = 1: u2 = P/k | book | 1 | 1 | 0.00 % | ✅ |
| penalty alpha = 10: u1 = (2+alpha)P/((1+alpha)k) | book | 1.0909 | 1.0909 | 0.00 % | ✅ |
| penalty alpha = 10: u2 = P/k | book | 1 | 1 | 0.00 % | ✅ |
| penalty alpha = 1e+06: u1 = (2+alpha)P/((1+alpha)k) | book | 1 | 1 | 0.00 % | ✅ |
| penalty alpha = 1e+06: u2 = P/k | book | 1 | 1 | 0.00 % | ✅ |

*Code*: `validation/books/cookmodeling/ch04.py`

## Chapter 5

<a id="cookmod-5-5-homogenized-perforated-plate"></a>

### Sez. 5.5, Fig. 5.5-1, Eqq. 5.5-3...5.5-7 - Equivalent elastic properties of a perforated plate from a repeating cell

📐 · PDF page 125 · plane_stress, homogenization

Procedure to 'smear' a regular perforation: a cell is isolated, three unit constant-strain states (eps_x, eps_y, gamma_xy) are imposed with symmetry and antisymmetry conditions on the sides, and the reactions give the elasticity matrix of the equivalent plate, E = D^T [R1 R2 R3] / V. The book gives no numbers.

**Model**: Repeating a x b cell with a quarter hole, mapped QM6 mesh; the three unit strain states are imposed with settlements (add_settlement) on the sides as in the book's table; E_ij = D_i^T R_j / V with feagent displacements and reactions.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| solid cell: E11 = plane stress | exact | 1098.9 | 1098.9 | 0.00 % | ✅ |
| solid cell: E12 = plane stress | exact | 329.67 | 329.67 | 0.00 % | ✅ |
| solid cell: E22 = plane stress | exact | 1098.9 | 1098.9 | 0.00 % | ✅ |
| solid cell: E33 = plane stress | exact | 384.62 | 384.62 | 0.00 % | ✅ |
| solid cell: E13 = plane stress | exact | 0 | 1.3006e-12 | 0.00 % | ✅ |
| perforated cell: E12 / E21 = 1 (reciprocity) | exact | 1 | 1 | 0.00 % | ✅ |
| perforated cell: E11 / E22 = 1 (cell symmetry) | exact | 1 | 1 | 0.00 % | ✅ |
| E13 of the cell + E13 of the mirrored cell = 0 (orthotropy over the period) | exact | 0 | 1.5490e-12 | 0.00 % | ✅ |
| E33 equal in the mirrored cell | exact | 186.59 | 186.59 | 0.00 % | ✅ |
| E11 with doubled mesh versus base mesh | other | 665.06 | 664.25 | 0.12 % | ✅ |
| E33 with doubled mesh versus base mesh | other | 186.59 | 185.85 | 0.39 % | ✅ |
| relative reduction of shear larger than that of E11 (1 = true) | other | 1 | 1 | 0.00 % | ✅ |

**Notes**: Data chosen: 10 x 10 square cell, hole radius 5 (one quarter per cell), E = 1000, nu = 0.3, plane stress. The D_i are the affine fields of the unit states (u = x, v = y, u = y) evaluated at the nodes. Checks: with a solid cell the procedure returns exactly the plane-stress matrix; with the hole E12 = E21 and E11 = E22 (cell symmetry); the shear modulus drops more than the normal moduli; the result is stable when the mesh is doubled (within 0.5 %). A caveat not written in the book: with a cell containing a quarter hole the coupling terms E13 and E23 are not zero (about -97 against 665), because the cell is not symmetric about its own centre; they change sign in the mirrored cell and cancel over the whole period, so the equivalent plate is orthotropic and those terms must be set to zero.

*Code*: `validation/books/cookmodeling/ch05.py`

<a id="cookmod-5-7-bolt-pretension-smeared"></a>

### Sez. 5.7, Fig. 5.7-3, Eqq. 5.7-1, 5.7-2 - Smeared flange bolts and pretension by cooling

📐 · PDF page 129 · bar, thermal_stress

In an axisymmetric flange joint the n bolts are replaced by an equivalent solid of equal axial stiffness and the pretension P is obtained by cooling it by DeltaT, with alpha L DeltaT = P L / (A_r E_r) + P L / (A_f E_f). Data chosen: 8 bolts d = 16 mm, E_b = 200 GPa, L = 40 mm, alpha = 12e-6, flange three times stiffer than the bolts, P = 400 kN.

**Model**: The bolts are an L2 bar between nodal circles A and B with A_r E_r = n (pi d^2 / 4) E_b; the flange as seen by the bolts is a spring of stiffness A_f E_f / L in parallel; cooling DeltaT of the bar only (add_solid_thermal).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| total bolt force = P [N] | exact | 4e+05 | 4e+05 | 0.00 % | ✅ |
| flange force = -P (compression) [N] | exact | -4e+05 | -4e+05 | 0.00 % | ✅ |
| stress in a single bolt = P / (n pi d^2 / 4) [MPa] | exact | 248.68 | 248.68 | 0.00 % | ✅ |
| flange shortening = P L / (A_f E_f) [mm] | exact | 0.016579 | 0.016579 | 0.00 % | ✅ |

**Notes**: With the DeltaT of Eq. 5.7-1 the force in the equivalent bar is exactly P, equal and opposite to the flange compression; the stress in a single bolt is the bar strain times E_b.

*Code*: `validation/books/cookmodeling/ch05.py`

<a id="cookmod-5-10-ill-conditioning-springs"></a>

### Sez. 5.10, Fig. 5.10-1, Eqq. 5.10-1, 5.10-2 - Ill-conditioning: stiff part supported by a flexible part

✅ · PDF page 137 · springs, numerics

With the stiff part k1 supported by the flexible k2, elimination gives (k1 + k2) - k1: with k1 = 1, k2 = 4.444444e-6 and seven digits 0.4e-5 is left, a single significant digit; with six digits the matrix becomes singular. The problem does not arise when the flexible part is supported by the stiff one.

**Model**: Two springs in series (add_spring) with k1 = 1 and k2 = 4.444444e-6 and the reversed configuration; the elimination step is repeated in 7- and 6-digit decimal arithmetic to reproduce the book's argument about significant digits.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u2 = P / k2 (double precision) | exact | 2.25e+05 | 2.25e+05 | 0.00 % | ✅ |
| u1 = P / k2 + P / k1 (double precision) | exact | 2.25e+05 | 2.25e+05 | 0.00 % | ✅ |
| pivot (k1 + k2) - k1 with 7 digits (book: 0.4e-5, one significant digit) | book | 4.0000e-06 | 4.0000e-06 | 0.00 % | ✅ |
| pivot with 6 digits: zero (singular matrix) | book | 0 | 0 | 0.00 % | ✅ |
| K22 = k1 + k2 assembled by feagent | book | 1 | 1 | 0.00 % | ✅ |
| flexible part supported by the stiff one: u2 = P / k1 | exact | 1 | 1 | 0.00 % | ✅ |
| k1/k2 = 1e17: matrix declared singular or solution unreliable (1 = true) | book | 1 | 1 | 0.00 % | ✅ |

**Notes**: In double precision feagent solves the book's case with a relative error of about 1e-11 and estimates its condition number; in 7-digit arithmetic the reduced pivot is 4e-6 instead of 4.444e-6 (one correct digit, as in the book), with 6 digits it is zero. With k2 = 1e-17 (ratio beyond 1e16) feagent declares the matrix singular, like the book's six-digit program.

*Code*: `validation/books/cookmodeling/ch05.py`

<a id="cookmod-5-10-folded-strip-ill-conditioning"></a>

### Sez. 5.10, Fig. 5.10-3 - Arch-like folded strip of flat shells: stress degradation as the thickness decreases

🟡 · PDF page 139 · shell, numerics

Thin arch-like structure, similar to a folded paper strip: membrane stiffness is much larger than bending stiffness and the system becomes ill-conditioned as the thickness decreases. The book reports that the x membrane stress in element A has a 5 % error for t = 0.6e-n, a severe error for t = 0.2e-n and a singular matrix for t = 0.1e-n (unreadable exponent, depending on software and computer).

**Model**: 7 ShellQ4 shells (membrane + bending) 5 wide on an arch rebuilt from the figure (two arcs of radius 10 with centres 5 apart, element A horizontal at the crown), clamped bases; forces 1 (towards -x) and 2 (towards +x) on the nodes of A.

![cookmod-5.10-folded-strip-ill-conditioning](images/books/cookmod-5.10-folded-strip-ill-conditioning.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Nx in element A, t = 0.01 (limit 3/5) | exact | 0.6 | 0.6 | 0.00 % | ✅ |
| Nx in element A, t = 0.001 (limit 3/5) | exact | 0.6 | 0.6 | 0.00 % | ✅ |
| Nx in element A, t = 0.0001 (limit 3/5) | exact | 0.6 | 0.6 | 0.00 % | ✅ |
| Nx in element A, t = 1e-05 (limit 3/5) | exact | 0.6 | 0.59999 | 0.00 % | ✅ |
| log10 of the condition-number ratio between t = 1e-5 and 1e-4 (about 2) | other | 2 | 1.9765 | 1.18 % | ✅ |
| t = 1e-6: solution flagged as unreliable (1 = true) | book | 1 | 1 | 0.00 % | ✅ |

**Notes**: Geometry read from the figure (arch nodes at 63.3 and 37.8 degrees, bases at x = -9.1 and 14.1), E = 1e7, nu = 0.3 chosen. For t -> 0 element A becomes rigid and the membrane force tends to Nx = 3 / 5 (symmetric part of the loads, self-equilibrated in A): this is the reference. In double precision feagent stays exact to 1e-6 down to t = 1e-4; at t = 1e-5 (estimated condition number about 6e13) the error is 2e-5, then it degrades: at t = 1e-6 the error is 1 % and the solution is flagged as unreliable. The condition number grows as 1/t^2. Since October 2026 the drilling spring of the shells is referred to the bending stiffness (before, to the membrane one, which restrained rigid rotations in thin shells): at extreme thicknesses it weighs less, and the error at t = 1e-5 and 1e-6 went up from 1e-5 and 0.2 %. The book's thresholds (single precision) cannot be compared.

*Code*: `validation/books/cookmodeling/ch05.py`

<a id="cookmod-5-14-scf-correction-factor"></a>

### Sez. 5.14, Fig. 5.14-1 - Correction factor from a secondary case for the stress near a hole

🟡 · PDF page 146 · plane_stress

To correct the peak stress of a coarse mesh, a secondary case with a known solution is solved with the same local mesh and the result is scaled by the exact/computed ratio. In the book: primary 221, secondary 92.8 against 130 exact, factor 1.40, estimate 310 against 337 from a very fine mesh (arbitrary units).

**Model**: Q4 on one quadrant: local 2 x 2 mesh rebuilt from the figure (thin ring between r = 0.5 and 0.67 a, then out to the boundary) in the holed square loaded by concentrated forces and, identical, in the tensile strip of the secondary case (plus one element to the end, x = 2.34 a); 32 x 16 mapped meshes as the 'exact' solution. Stress at E from nodal averages.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| primary case: 2 x 2 mesh / exact solution (book 221/337) | book | 0.65579 | 0.68199 | 4.00 % | ✅ |
| secondary case: 2 x 2 mesh / exact (book 92.8/130) | book | 0.71385 | 0.73504 | 2.97 % | ✅ |
| correction factor exact/computed of the secondary case | book | 1.4 | 1.3605 | 2.82 % | ✅ |
| corrected primary estimate / exact (book 310/337) | book | 0.91988 | 0.92782 | 0.86 % | ✅ |
| secondary: gross-section K versus Peterson's formula | exact | 4.3117 | 4.3526 | 0.95 % | ✅ |

**Notes**: Dimensions and loads are not printed: from the figure the hole radius is half the half-side and the strip has the height of the square, length 2 x 2.34 times the half-side; dimensionless ratios are therefore compared. The 'exact' secondary solution (32 x 16) gives K = 4.35 on the gross section, against 4.31 from Peterson's formula for d/W = 0.5. With Q4 the ratios are within 4 % of the book (factor 1.36 against 1.40; corrected estimate / exact 0.93 against 0.92).

*Code*: `validation/books/cookmodeling/ch05.py`

<a id="cookmod-5-15-richardson-extrapolation"></a>

### Sez. 5.15, Eq. 5.15-1, Fig. 5.15-2 - Extrapolation to h -> 0 from two meshes

📐 · PDF page 150 · bar, plane_stress

If the error is proportional to h^q, two analyses with steps h1 and h2 give phi_0 = (phi1 h2^q - phi2 h1^q) / (h2^q - h1^q). The book warns that with non-monotonic convergence the extrapolation may worsen the result.

**Model**: Book formula applied to feagent results: stepped tapered bar of Sec. 1.4 (error in h^2), thick cylinder with axisymmetric Q4, oval ring of Sec. 2.7.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| bar: extrapolation from 4 and 8 steps | exact | 0.027465 | 0.027463 | 0.01 % | ✅ |
| thick cylinder: u(a) with 8 Q4 | exact | 0.0019067 | 0.0019018 | 0.26 % | ✅ |
| thick cylinder: u(a) extrapolated from 4 and 8 Q4 with q = 2 | exact | 0.0019067 | 0.0019065 | 0.01 % | ✅ |
| oval ring (Sec. 2.7), u_A with 4, 8, 16, 256 chords: non-monotonic convergence (1 = true) | book | 1 | 1 | 0.00 % | ✅ |

**Notes**: Bar: with 4 and 8 steps and q = 2 the error drops from 0.2 % to below 1e-4. Thick cylinder under pressure (Lame solution) with axisymmetric Q4: the inner displacement with 8 elements is 0.25 % off, the one extrapolated from 4 and 8 elements 0.01 %. The oval ring of Sec. 2.7, refining the arc chords, converges non-monotonically: the case in which the book advises against extrapolation.

*Code*: `validation/books/cookmodeling/ch05.py`

<a id="cookmod-5-16-zz-error-adaptive"></a>

### Sez. 5.16, Figg. 5.16-1, 5.16-2, Eqq. 5.16-1, 5.16-2 - Z2 energy error estimator and guided refinement

🟡 · PDF page 151 · plane_stress, error_estimation

The Zienkiewicz-Zhu estimator compares the discontinuous element stress field with the continuous one obtained by nodal averaging: eta = sqrt(U_e / (U + U_e)). In the uniformly loaded bar the averaged field is exact except in the end elements. The book reports eta = 0.373 and 0.183 for the two meshes of the holed plate and an adaptive example with LST triangles (40, 228, 286 dof; eta = 0.27, 0.07, 0.04) of unquoted geometry.

**Model**: feagent.error_estimation.zz_error (nodal average and SPR). Fig. 5.16-1: strip of 6 Q4 with nu = 0 under a uniform axial body load (equivalent to the bar). Holed plate of Sec. 3.12: rebuilt coarse mesh and increasingly fine mapped meshes, with the element error map.

![cookmod-5.16-zz-error-adaptive](images/books/cookmod-5.16-zz-error-adaptive.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| bar: averaged stress at interior node x = 1 (exact) | exact | 5 | 5 | 0.00 % | ✅ |
| bar: averaged stress at interior node x = 3 (exact) | exact | 3 | 3 | 0.00 % | ✅ |
| bar: averaged stress at interior node x = 5 (exact) | exact | 1 | 1 | 0.00 % | ✅ |
| bar: at the clamp the average is the element value (5.5 against exact 6) | other | 5.5 | 5.5 | 0.00 % | ✅ |
| holed plate: eta decreases as the mesh is refined (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| holed plate: eta halves with the step (order about 1, Q4) | other | 1 | 1.0051 | 0.51 % | ✅ |
| 32 x 16 mesh: eta below the book's 0.05 target (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| holed plate, coarse mesh: eta with nodal average versus SPR | other | 0.26543 | 0.26404 | 0.52 % | ✅ |

**Notes**: The behaviour of Fig. 5.16-1 (averaged stress exact at the interior nodes) and the decrease of eta with the mesh down below the book's 0.05 target are reproduced. On the rebuilt coarse mesh of the holed plate feagent estimates eta = 0.26 (average and SPR) against the book's 0.373: the gap cannot be resolved without knowing how the book's program builds the fields for the Q6 (node by node or from Gauss points, incompatible modes included or not); the book value is therefore not compared. The adaptive example of Fig. 5.16-2 cannot be redone (geometry and loads not quoted).

*Code*: `validation/books/cookmodeling/ch05.py`

## Chapter 6

<a id="cookmod-6-3-curved-beam-3d"></a>

### Sez. 6.3 A 3D Application, Figg. 6.3-1, 6.3-2, Eq. 6.3-1 - Curved beam of trapezoidal section in pure bending with Hex8 solids

✅ · PDF page 162 · solid_3d

Curved beam (inner radius 44 mm, radial depth 88 mm, trapezoidal section 130 mm wide inside and 28 mm outside, steel) in pure in-plane bending. The moment is applied by rotating one face of the wedge about r_c, chosen to cancel the radial reaction at A (60 mm: 2001 N, 70 mm: 357 N, extrapolated 72.2 mm). Book results: M = 8.804e6 N mm, sigma_theta = 146 MPa at A and 65 MPa at E, against 119 MPa from curved-beam theory.

**Model**: One layer of Hex8 with incompatible modes in a 5-degree wedge, 8 x 6 mesh graded towards the inner edge on the half section. Face 1: v = 0, u = 0 at A, w = 0 on the symmetry plane; face 2: rotated support axes (set_support_axes) and circumferential settlement v = 0.0001 (r_c - r) with add_settlement.

![cookmod-6.3-curved-beam-3d](images/books/cookmod-6.3-curved-beam-3d.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| r_c cancelling the radial reaction at A [mm] | book | 72.2 | 72.137 | 0.09 % | ✅ |
| radial reaction at A with r_c [N] | exact | 0 | 2.2737e-13 | 0.00 % | ✅ |
| moment M from the face-1 reactions [N mm] | book | 8.8040e+06 | 8.7995e+06 | 0.05 % | ✅ |
| sigma_theta at A [MPa] | book | 146 | 146.23 | 0.16 % | ✅ |
| sigma_theta at E [MPa] | book | 65 | 64.814 | 0.29 % | ✅ |
| curved-beam theory 13.46e-6 M [MPa] | book | 119 | 118.44 | 0.47 % | ✅ |
| maximum sigma_r (book: about 25) [MPa] | book | 25 | 24.841 | 0.64 % | ✅ |

**Notes**: The book's mesh is only drawn: the graded 8 x 6 mesh reproduces r_c, M and the stresses at A and E within 0.5 %. The radial reactions of the two trial analyses depend more on the mesh (feagent: 1823 and 321 N against the book's 2001 and 357 N; opposite sign by the reaction convention) and are not compared. Maximum sigma_r about 25 MPa as in the book.

*Code*: `validation/books/cookmodeling/ch06.py`

<a id="cookmod-6-5-shrink-fit-spinning-disks"></a>

### Sez. 6.5 An Axisymmetric Application, Figg. 6.5-1...6.5-4 - Tapered disk with a shrink-fitted ring, then spinning

🟡 · PDF page 167 · axisymmetric, thermal_stress

A constant-thickness ring is shrink-fitted on a tapered disk (interference equal to a 100-degree temperature difference) and the assembly spins. The book estimates the contact pressure with a thin ring (p = 95 MPa) and the loosening speed with the spinning solid disk (298 rad/s), then comments on the FE contours: sigma_r discontinuous along BE (-136 at B), ring sigma_theta up to 178 MPa, mean sigma_r about 100 MPa on BE when spinning at 298 rad/s.

**Model**: Axisymmetric Q8 in three layers: inner disk with 9 columns (r from 0 to 400 mm, thickness from 120 to 42 mm), ring with 8 columns (r from 400 to 700 mm, 42 mm thick), 20 mm elements on both sides of BE (mesh rebuilt from Fig. 6.5-1); shared nodes on BE (bonding). Shrink fit: DeltaT = -100 degrees per element on the ring only; spinning: consistent centrifugal forces omega^2 M_e {r} built with the elements' consistent mass.

![cookmod-6.5-shrink-fit-spinning-disks](images/books/cookmod-6.5-shrink-fit-spinning-disks.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| estimated shrink-fit pressure (Eq. 6.5-2) [MPa] | book | 95 | 94.737 | 0.28 % | ✅ |
| estimated loosening speed (Eq. 6.5-3) [rad/s] | book | 298 | 297.98 | 0.01 % | ✅ |
| shrink fit: mean pressure on BE (resultant) against the hand estimate 95 [MPa] | book | 95 | 83.738 | 11.86 % | ✅ |
| shrink fit: mean nodal -sigma_r on BE against the resultant [MPa] | other | 83.738 | 83.205 | 0.64 % | ✅ |
| shrink fit: maximum ring sigma_theta [MPa] | book | 178 | 173.56 | 2.49 % | ✅ |
| spinning at 298 rad/s: mean sigma_r along BE (book about 100) [MPa] | book | 100 | 98.257 | 1.74 % | ✅ |
| shrink fit: sigma_r jump across BE over 50 MPa (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| first-loosening speed on BE (Eq. 6.5-5, no book value) [rad/s] | book | - | 250.87 | - | ✅ |

**Notes**: Mesh rebuilt from the drawing and book results only as contour plots: comparisons with 10 % tolerances. Shrink fit: the mean pressure on BE, from the radial resultant of the ring's internal forces, is 84 MPa against the 95 of the thin-ring hand estimate (the book only claims approximate agreement; 15 % tolerance), with strong sigma_r jumps between the two sides as in Fig. 6.5-3a; maximum ring sigma_theta at element centroids 174 MPa (book 178). Spinning: mean sigma_r on BE about 99 MPa. The book's eta estimates (0.28; 0.17; 0.09; 0.004) depend on the mesh and are not compared (feagent: 0.17; 0.05; 0.02; 0.016). feagent has no native centrifugal load yet: the nodal forces are built in the case from the element consistent mass.

*Code*: `validation/books/cookmodeling/ch06.py`

## Chapter 7

<a id="cookmod-7-1-membrane-string-action"></a>

### Sez. 7.1, Fig. 7.1-4, Eqq. 7.1-5, 7.1-6 - Beam with immovable hinges: share of load carried by string action

✅ · PDF page 184 · beam_2d, geometric_nonlinear

A beam on hinges that cannot approach each other develops a tensile axial force that carries part of the load ('string action'). The book approximates q = (E b t^4 / L^4) [21.3 (w_c/t)^3 + 6.40 (w_c/t)] and notes that for w_c/t = 0.5 string and bending each carry about half of the load. Data chosen: L = 200, b = 10, t = 2, E = 200000.

**Model**: 40 corotational Euler-Bernoulli beams (feagent.corotational.solve_corotational) with displacement control at midspan; immovable hinges at the ends, uniform load as nodal forces.

![cookmod-7.1-membrane-string-action](images/books/cookmod-7.1-membrane-string-action.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| load for w_c/t = 0.5: corotational versus exact solution | exact | 0.11289 | 0.11288 | 0.01 % | ✅ |
| load for w_c/t = 0.5 versus the approximate formula 7.1-6 (5 %) | book | 0.11725 | 0.11288 | 3.72 % | ✅ |
| load for w_c/t = 2.0: corotational versus exact solution | exact | 3.5377 | 3.5315 | 0.17 % | ✅ |
| load for w_c/t = 2.0 versus the approximate formula 7.1-6 (5 %) | book | 3.664 | 3.5315 | 3.62 % | ✅ |
| w_c/t = 0.5: share carried by bending (book: about half) | book | 0.5 | 0.56696 | 13.39 % | ✅ |

**Notes**: The book's formula adds two different deflected shapes and is approximate: against the exact tie-beam solution (von Karman theory, N from the length constraint) it overestimates the load by 4 % at w_c/t = 0.5 and by 4 % at w_c/t = 2. feagent's corotational solution matches the exact one within 0.2 %. At w_c/t = 0.5 bending alone would carry 57 % of the load.

*Code*: `validation/books/cookmodeling/ch07.py`

<a id="cookmod-7-2-square-plate-benchmarks"></a>

### Sez. 7.2 Test Cases, Fig. 7.2-6a - Simply supported or clamped square plate: centre deflection

✅ · PDF page 190 · plate

Square plate of side L, nu = 0.3, simply supported or clamped, under uniform load q or a central force P: w = alpha q L^4 / D or alpha P L^2 / D with alpha = 0.00406; 0.0116; 0.00126; 0.00560.

**Model**: Quadrant with a uniform 16 x 16 mesh: thin DKT triangles (add_shell_tri formulation='thin') for all cases; Mindlin Q4 (add_shell) for the uniform load. 'Soft' simple support (w = 0 only), clamp w = theta = 0, symmetry on the inner sides.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| alpha, simply supported, uniform load, DKT | book | 0.00406 | 0.0040588 | 0.03 % | ✅ |
| alpha, simply supported, uniform load, Mindlin Q4 | book | 0.00406 | 0.0040747 | 0.36 % | ✅ |
| alpha, simply supported, central force, DKT | book | 0.0116 | 0.011614 | 0.12 % | ✅ |
| alpha, clamped, uniform load, DKT | exact | 0.001265 | 0.0012676 | 0.20 % | ✅ |
| alpha, clamped, uniform load, Mindlin Q4 | exact | 0.001265 | 0.0012669 | 0.15 % | ✅ |
| alpha, clamped, central force, DKT | exact | 0.005612 | 0.0056254 | 0.24 % | ✅ |

**Notes**: For the clamped plates the book prints 0.00126 and 0.00560: the comparison uses Timoshenko's four-digit values (0.001265 and 0.005612). The DKT triangles converge within 0.3 % of the book values with 16 x 16 squares on the quadrant. The Mindlin Q4 (t/L = 0.01) give 1 % more under uniform load; with the concentrated force they are not compared, because in Mindlin theory the deflection under the load does not converge (as the book recalls in Sec. 7.1).

*Code*: `validation/books/cookmodeling/ch07.py`

<a id="cookmod-7-2-rhombic-plate"></a>

### Sez. 7.2 Test Cases, Fig. 7.2-6b - 30-degree rhombic plate, simply supported, uniform load

✅ · PDF page 190 · plate

Rhombus with a 30-degree acute angle, simply supported, uniform load: at the obtuse corner the moments are infinite and of opposite sign; the book recommends the 'soft' support with Mindlin elements because the 'hard' one may give badly wrong deflections. The book gives no numbers.

**Model**: n x n mesh parallel to the sides of the rhombus (n = 8, 16, 32) of DKT triangles and Mindlin Q4; 'soft' support (w = 0) and, for the Q4, also 'hard' (rotation about the edge normal fixed with rotated support axes).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| centre deflection, extrapolated DKT (Morley 4.08e-4) | exact | 4.0800e-04 | 4.1044e-04 | 0.60 % | ✅ |
| centre deflection, DKT 32 x 32 | exact | 4.0800e-04 | 4.1906e-04 | 2.71 % | ✅ |
| Mindlin Q4 24 x 24: 'hard' support deflection below 90 % of the 'soft' one (1 = true) | book | 1 | 1 | 0.00 % | ✅ |

**Notes**: Literature reference (Morley's solution for nu = 0.3, cited by the book through [7.2]): w_c = 4.08e-4 q L^4 / D. The DKT converge from above (8: 5.00; 16: 4.38; 32: 4.19 e-4); Richardson extrapolation with the order estimated from the three meshes gives 4.10e-4 (0.5 %). With Mindlin Q4 the 'hard' support gives at n = 24 a deflection 17 % lower than the 'soft' one, as the book warns.

*Code*: `validation/books/cookmodeling/ch07.py`

<a id="cookmod-7-2-twisted-strip-plate"></a>

### Sez. 7.2 Test Cases, Fig. 7.2-6c,d - Twisting of a flat cantilever strip

✅ · PDF page 190 · plate

Flat cantilever strip twisted by a unit end torque, applied as a pair of vertical forces or as nodal moments. With E = 1e7, nu = 0.25, t = 0.05 the reference value is w3 = 0.0029 L. One or two elements are used to test the effect of shape.

**Model**: Strip 1 wide and L = 12 long, clamped: two DKT triangles, one Mindlin Q4, a 32 x 8 DKT mesh as the converged solution; load (c) opposite vertical forces at the free corners, load (d) nodal moments 0.5 about the axis.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| w3 / L, 32 x 8 DKT mesh, load (c) | book | 0.0029 | 0.0029272 | 0.94 % | ✅ |
| w3 / L, 32 x 8 DKT mesh, load (d) | book | 0.0029 | 0.0029272 | 0.94 % | ✅ |
| w3 / L, Mindlin Q4 8 x 2 | book | 0.0029 | 0.0029299 | 1.03 % | ✅ |
| w3 / L with two DKT triangles (no printed value) | book | - | 0.0032781 | - | ✅ |
| w3 / L with a single Mindlin Q4 (MITC4) | book | 0.0029 | 0.0027758 | 4.28 % | ✅ |

**Notes**: The book does not give L: since w3/L is independent of L only for long strips, L = 12 is assumed (the 12 x 1 strip of benchmark [7.1]); with L = 1 the converged solution would be 0.0023 L. The fine DKT mesh gives 0.00293 L (Saint-Venant theory 0.0030 L). Two DKT give 0.0033 L. A single Mindlin Q4 gives 0.00278 L (-4 %). Until October 2026 the Q4 integrated the shear at a single point: a single row of elements across the width had a near zero-energy twisting mode and gave 0.047 L, 16 times too much; with the MITC4 assumed shear (Bathe and Dvorkin 1985) the mode is gone.

*Code*: `validation/books/cookmodeling/ch07.py`

<a id="cookmod-7-3-plate-winkler-point-load"></a>

### Sez. 7.3 A Plate Application, Figg. 7.3-1...7.3-3 - Plate on a Winkler foundation under a point load

✅ · PDF page 191 · plate, foundation

Large plate (t = 30 mm, steel) on a Winkler foundation k0 = 0.2 MPa/mm loaded by P = 1 N. Preliminary estimate with a cosine shape: 10.2e-6 mm. The book finds 12.7e-6 mm under the load, zero deflection at about 825 mm and a maximum uplift of 0.2e-6 mm.

**Model**: 11 x 11 quadrant of 150 mm squares, each of four overlapping DKT triangles with halved E and k (equivalent to the book's sum divided by two); foundation with feagent.foundation.add_plate_foundation (stiffness lumped at the nodes); symmetry theta = 0 on the axes, P/4 at the origin.

![cookmod-7.3-plate-winkler-point-load](images/books/cookmod-7.3-plate-winkler-point-load.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| deflection under the load [1e-6 mm] | book | 12.7 | 12.711 | 0.08 % | ✅ |
| deflection versus the infinite plate P / (8 sqrt(k D)) [mm] | exact | 1.2569e-05 | 1.2711e-05 | 1.12 % | ✅ |
| abscissa of the zero crossing [mm] | book | 825 | 878.83 | 6.52 % | ✅ |
| maximum uplift along the x axis [1e-6 mm] | book | 0.2 | 0.18197 | 9.01 % | ✅ |
| preliminary estimate P / ((pi/2 - 2/pi) k0 R^2) [1e-6 mm] | book | 10.2 | 10.183 | 0.16 % | ✅ |

**Notes**: With the foundation lumped at the nodes the deflection matches the book (12.71e-6 mm); with the consistent stiffness it would be 13.0e-6. The infinite plate solution P / (8 sqrt(k D)) = 12.57e-6 mm confirms the value. The zero crossing, linearly interpolated between nodes as in the book's plot, falls at 879 mm against 'about 825' (read from the plot): 7 % tolerance. The foundation is two-sided (it pulls where the plate lifts), as in the book.

*Code*: `validation/books/cookmodeling/ch07.py`

<a id="cookmod-7-4-boundary-layer-length"></a>

### Sez. 7.4, Eq. 7.4-1 - Width of the bending boundary layer in shells

📐 · PDF page 195 · axisymmetric, shell

With a line load at the end of a cylinder the bending response decays as e^(-lambda x) cos(lambda x), lambda = [3 (1 - nu^2)]^(1/4) / sqrt(R t): at lambda x = 3 pi / 4 7 % is left. The book uses sqrt(R t) as the width of the boundary layer (1.8 mm for the can).

**Model**: Cylindrical can (R = 33 mm, t = 0.1 mm) modelled with 200 axisymmetric Q8 through the thickness over 20 mm, radial line load at the end; comparison with the cylinder solution e^(-lambda x) cos(lambda x).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| sqrt(R t) for the can [mm] | book | 1.8 | 1.8166 | 0.92 % | ✅ |
| |e^(-lambda x) cos(lambda x)| at lambda x = 3 pi / 4 | book | 0.07 | 0.06702 | 4.26 % | ✅ |
| end deflection Q / (2 lambda^3 D) [mm] | exact | 0.077057 | 0.077087 | 0.04 % | ✅ |
| first zero of the deflection, pi / (2 lambda) [mm] | exact | 2.2199 | 2.22 | 0.01 % | ✅ |
| ratio w / w0 at lambda x = 3 pi / 4 | exact | -0.06702 | -0.066955 | 0.10 % | ✅ |

**Notes**: feagent reproduces the cylinder solution: end deflection Q / (2 lambda^3 D), first zero at pi / (2 lambda), ratio -0.067 at lambda x = 3 pi / 4. Note: with the book's formula lambda x = 3 pi / 4 corresponds to x = 1.8 sqrt(R t) (about 3.3 mm for the can), not to sqrt(R t); sqrt(R t) is therefore a somewhat tight rule of thumb, valid for the order of magnitude.

*Code*: `validation/books/cookmodeling/ch07.py`

<a id="cookmod-7-5-shell-obstacle-course"></a>

### Sez. 7.5 Test Cases, Fig. 7.5-2 - Shell benchmarks: cylindrical roof, pinched cylinder, hemisphere, twisted beam

✅ · PDF page 198 · shell

The MacNeal-Harder 'obstacle course': cylindrical roof under self weight (0.3024), pinched cylinder (0.1825e-4), hemisphere with four radial forces (0.0924), beam with a 90-degree twist with t = 0.0032 and forces 1e-6 (5256e-6 and 1294e-6).

**Model**: Thin DKT triangles with GT9 membrane (add_shell_tri formulation='thin'): Scordelis-Lo roof on a 16 x 16 quarter, pinched cylinder on a 24 x 24 eighth, quarter hemisphere subdivided into 32^2 triangles, 24 x 4 twisted beam (48 x 8 in the thin case). MITC4 Mindlin Q4 (add_shell) on roof, cylinder and 24 x 4 twisted beam.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Scordelis-Lo roof, DKT 16 x 16 | book | 0.3024 | 0.29874 | 1.21 % | ✅ |
| pinched cylinder, DKT 24 x 24 | book | 1.8250e-05 | 1.8297e-05 | 0.26 % | ✅ |
| hemisphere, DKT 32^2 | book | 0.0924 | 0.091339 | 1.15 % | ✅ |
| twisted beam, t = 0.32, in-plane tip load (MacNeal-Harder) | other | 0.005424 | 0.0053874 | 0.67 % | ✅ |
| twisted beam, t = 0.32, out-of-plane tip load (MacNeal-Harder) | other | 0.001754 | 0.0017205 | 1.91 % | ✅ |
| book's twisted beam (t = 0.0032, F = 1e-6), in-plane load, DKT 48 x 8 | book | 0.005256 | 0.0050396 | 4.12 % | ✅ |
| book's twisted beam, out-of-plane load, DKT 48 x 8 | book | 0.001294 | 0.0012516 | 3.28 % | ✅ |
| book's twisted beam, in-plane load, DKT 24 x 4 (still stiff, see notes) | book | - | 0.0033491 | - | ✅ |
| Scordelis-Lo roof, MITC4 Q4 16 x 16 | book | 0.3024 | 0.30468 | 0.76 % | ✅ |
| pinched cylinder, MITC4 Q4 24 x 24 | book | 1.8250e-05 | 1.7768e-05 | 2.64 % | ✅ |
| twisted beam, t = 0.32, in-plane load, MITC4 Q4 24 x 4 | other | 0.005424 | 0.0054035 | 0.38 % | ✅ |
| twisted beam, t = 0.32, out-of-plane load, MITC4 Q4 24 x 4 | other | 0.001754 | 0.0017165 | 2.14 % | ✅ |
| book's twisted beam, in-plane load, MITC4 Q4 24 x 4 | book | 0.005256 | 0.0050899 | 3.16 % | ✅ |
| book's twisted beam, out-of-plane load, MITC4 Q4 24 x 4 | book | 0.001294 | 0.0012716 | 1.73 % | ✅ |

**Notes**: With DKT: roof -1.2 % (32 x 32: -0.7 %, flat shells converge to about 0.300), cylinder +0.3 %, hemisphere -1.1 % (slow convergence from below: 48^2 triangles give -0.3 %). Twisted beam: with MacNeal and Harder's original thickness (t = 0.32, F = 1: 5.424e-3 and 1.754e-3) the DKT are within 2 %; in the book's thin case (t = 0.0032) the 24 x 4 DKT are still about 35 % too stiff (0.64 and 0.68 of the reference) and converge with refinement (48 x 8: -4 % and -3 %). The MITC4 Q4: roof +0.8 %, cylinder -2.6 % (convergence from below), twisted beam -0.4 % and -2.1 %, thin case -3.2 % and -1.7 %. Corrections of October 2026 that came out of this case: the Q4 integrated the shear at one point (spurious modes: roof 0.453, +50 %; cylinder above the reference and growing) and had neither rigid warping offsets nor a drilling link in warped elements (diverging twisted beam); the stabilising drilling stiffness of Q4 and triangles was referred to the membrane stiffness and restrained rigid rotations; the GT9 triangles read the bending of non-coplanar neighbours as in-plane rotation (thin twisted beam 10-30 times too stiff, now read about the nodal normal).

*Code*: `validation/books/cookmodeling/ch07.py`

<a id="cookmod-7-5-conical-frusta-sphere"></a>

### Sez. 7.5, Fig. 7.5-3 - Pressurized sphere modelled with conical frusta: spurious bending moments

🟡 · PDF page 199 · shell

A pressurized sphere has no bending moments, but modelled with a few conical frusta it shows spurious moments that vanish as the mesh is refined. The book gives no dimensions or numbers.

**Model**: feagent has no axisymmetric shell elements: a 2-degree wedge of flat DKT triangles is used (each band is a conical frustum), with rotated support axes for cyclic symmetry; half sphere R = 100, t = 1, p = 1 with 4, 8, 16 and 32 facets along the meridian.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| membrane force at the equator, 32 facets (p R / 2) [N/mm] | exact | 50 | 50.057 | 0.11 % | ✅ |
| spurious moment in the lower half: 4 facets over 5 times 16 facets (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| maximum spurious moment in the lower half, 4 facets [N mm/mm] | book | - | 0.25283 | - | ✅ |

**Notes**: Data chosen. The membrane force converges to p R / 2 = 50; the maximum spurious moment in the lower half (away from the pole, where the wedge degenerates) drops from 0.25 with 4 facets to 0.03 with 16 (6 M / t^2 from 3 % to 0.3 % of the membrane stress). The element is not the book's one (axisymmetric cone).

*Code*: `validation/books/cookmodeling/ch07.py`

<a id="cookmod-7-6-spherical-segment-axisym-shell"></a>

### Sez. 7.6 An Axisymmetric Shell Application, Figg. 7.6-1, 7.6-2 - Spherical zone clamped at the equator with a line load on the upper edge

🟡 · PDF page 200 · shell

Spherical zone (R = 800 mm, t = 1 mm, steel) from the clamped equator to the 30-degree parallel, open at the top and loaded by 5 N/mm downwards on the edge. The book (axisymmetric shell elements) finds: membrane -4.33 MPa at the base, maximum meridional moment -17.7 N mm/mm 17 mm from the edge, M_B = 1.70, sigma_theta = -175 MPa at A.

**Model**: 0.5-degree wedge of DKT triangles with 64 elements along the meridian graded from 4 mm (ends) to 20 mm (centre), like the book's mesh; cyclic symmetry with rotated support axes; clamped base, vertical load 5 N/mm on the upper edge. M_B and the vertical force from the base reactions.

![cookmod-7.6-spherical-segment-axisym-shell](images/books/cookmod-7.6-spherical-segment-axisym-shell.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| vertical base force per unit length (0.866 q) [N/mm] | exact | 4.33 | 4.3301 | 0.00 % | ✅ |
| meridional membrane stress at the base (compression) [MPa] | book | 4.33 | 4.2963 | 0.78 % | ✅ |
| maximum meridional moment near the edge [N mm/mm] | book | 17.7 | 17.434 | 1.50 % | ✅ |
| distance of the peak moment from the top edge [mm] | book | 17 | 16.088 | 5.36 % | ✅ |
| moment at the clamp [N mm/mm] | book | 1.7 | 1.7016 | 0.10 % | ✅ |
| circumferential stress near A [MPa] | book | -175 | -172.71 | 1.31 % | ✅ |
| maximum bending stress 6 M / t^2 [MPa] | book | 106 | 104.6 | 1.32 % | ✅ |

**Notes**: feagent has no axisymmetric shells: the wedge of flat shells is a different element, hence 'partial' although the results agree: M_max -17.4 at 16 mm (with 128 elements -17.5 at 17 mm), M_B = 1.70, N_B = 4.33, sigma_theta at A -173 MPa at the centroid of the edge triangle (-174 with 128 elements). The sign of the moments depends on the local frame of the triangles.

*Code*: `validation/books/cookmodeling/ch07.py`

<a id="cookmod-7-7-slit-tube-cantilever"></a>

### Sez. 7.7 A General Shell Application, Figg. 7.7-1...7.7-3 - Thin tube slit lengthwise, cantilevered, under a transverse force

🟡 · PDF page 202 · shell

Tube of mean radius 25 mm, 2 mm thick, 280 mm long, slit along a generator, clamped and loaded by P = 1 N transversely at the centre of the end disk. Besides bending (373e-6 mm) the tube twists about the shear centre at x = -2R with warping restrained at the clamp: theory theta = -11.2e-6 rad, v = -933e-6 mm, sigma_z = 0.169 MPa. Book FE: deflections 682e-6 and 1252e-6 mm at the sides, mean 967e-6, theta = -11.4e-6, shear centre at -45.6 mm.

**Model**: Half tube (antisymmetry) with a 10 x 14 mesh as in the book: each quadrilateral is made of four overlapping thin triangles (GT9 membrane + DKT, halved E) instead of the book's Q6 + four DKT; end half-disk of 10 triangles; clamp at z = 0, u = w = theta_y = 0 on the continuous generator at y = 0, slit free. 20 x 28 mesh as converged reference.

![cookmod-7.7-slit-tube-cantilever](images/books/cookmod-7.7-slit-tube-cantilever.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| beam deflection P L^3 / (3 E I) [1e-6 mm] | book | 373 | 372.67 | 0.09 % | ✅ |
| bending P L R / I [MPa] | book | 0.0713 | 0.071301 | 0.00 % | ✅ |
| rotation with restrained warping [1e-6 rad] | book | -11.2 | -11.186 | 0.13 % | ✅ |
| sigma_z from restrained warping [MPa] | book | 0.169 | 0.16901 | 0.01 % | ✅ |
| deflection at x = +R, book mesh [1e-6 mm] | book | 682 | 642.33 | 5.82 % | ✅ |
| deflection at x = -R, book mesh [1e-6 mm] | book | 1252 | 1161.9 | 7.20 % | ✅ |
| end rotation, book mesh [1e-6 rad] | book | -11.4 | -10.392 | 8.85 % | ✅ |
| mean deflection, 20 x 28 mesh, versus theory [1e-6 mm] | exact | 933 | 918.7 | 1.53 % | ✅ |
| abscissa of the model shear centre [mm] | book | -45.6 | -45.198 | 0.88 % | ✅ |

**Notes**: The restrained-warping torsion formulas are verified (theta, v, sigma_z). With the book's mesh feagent gives 642e-6 and 1162e-6 mm (mean 902e-6, -7 % from the book) and theta = 10.4e-6: the element differs (the book's Q6 membrane is more flexible on the coarse mesh); with the 20 x 28 mesh the mean converges to 922e-6 (-1.2 % from theory, 933e-6). The model shear centre, from the second load equivalent to 0.5 N at x = -2R, lies at -45.2 mm (book -45.6). 10 % tolerances on the coarse-mesh values.

*Code*: `validation/books/cookmodeling/ch07.py`

## Chapter 8

<a id="cookmod-8-2-bar-conductivity"></a>

### Sez. 8.2, Fig. 8.2-1, Eq. 8.2-1 - Conductivity matrix of a bar with insulated lateral surface

📐 · PDF page 218 · heat_steady

For a bar of section A, length L and conductivity k with insulated lateral surface, the conductivity matrix is (A k / L) [[1, -1], [-1, 1]], identical to the axial stiffness A E / L. Data chosen: A = 100 mm2, L = 50 mm, k = 0.045 W/(mm C).

**Model**: L2 element of FieldModel('heat') (conduction()), compared with the book formula and with the stiffness of the structural L2 bar (add_bar) with E = k; stepped tapered bar split into elements for the heat flow.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| k11 = A k / L [W/C] | exact | 0.09 | 0.09 | 0.00 % | ✅ |
| k12 = -A k / L [W/C] | exact | -0.09 | -0.09 | 0.00 % | ✅ |
| difference from the axial stiffness of the bar with E = k | exact | 0 | 0 | 0.00 % | ✅ |
| heat flow of two segments in series [W] | exact | 5 | 5 | 0.00 % | ✅ |

**Notes**: Matrix verified term by term; the analogy with the structural bar is exact. For a bar with two segments of different section the heat flow between ends at prescribed temperature matches that of resistances in series.

*Code*: `validation/books/cookmodeling/ch08.py`

<a id="cookmod-8-4-crank-nicolson-scheme"></a>

### Sez. 8.4, Eqq. 8.4-1...8.4-5 (Problem 8.11) - Time integration of thermal transients with the generalized trapezoidal method

📐 · PDF page 222 · heat_transient

The book integrates K T + C dT/dt = Q with (gamma K + C/dt) T_n+1 = (1 - gamma) Q_n + gamma Q_n+1 - [(1 - gamma) K - C/dt] T_n: unconditionally stable for gamma >= 1/2, second order for gamma = 1/2 (Crank-Nicolson). Problem 8.11 (2 dT/dt + 6T = 8) has no printed answer.

**Model**: FieldModel.solve_transient (theta method) on a one-dof system built with an L2 element: 2 dT/dt + 6 T = 8 (Problem 8.11), compared with the recursion of Eq. 8.4-5 written with numpy and with the exact solution.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| maximum difference between feagent and the book recursion [C] | exact | 0 | 2.2204e-16 | 0.00 % | ✅ |
| Crank-Nicolson: error ratio halving dt | exact | 4 | 4.0067 | 0.17 % | ✅ |
| backward Euler: error ratio halving dt | exact | 2 | 2.011 | 0.55 % | ✅ |
| T(1) with Crank-Nicolson, dt = 0.05 [C] | exact | 1.267 | 1.2673 | 0.03 % | ✅ |
| dt = 1: Crank-Nicolson bounded, explicit divergent (1 = true) | book | 1 | 1 | 0.00 % | ✅ |

**Notes**: Initial condition T(0) = 0 chosen; exact solution T = 4/3 (1 - e^(-3t)). feagent matches the book recursion up to round-off; the Crank-Nicolson error drops 4 times when dt is halved, the backward Euler error 2 times; with dt = 1 the explicit method (gamma = 0) diverges while gamma = 1/2 stays bounded.

*Code*: `validation/books/cookmodeling/ch08.py`

<a id="cookmod-8-6-pipe-flange-thermal-stress"></a>

### Sez. 8.6 An Application, Figg. 8.6-1...8.6-5, Tab. 8.6-1 - Flange welded to a pipe: steady temperature field and thermal stresses

🟡 · PDF page 225 · heat_steady, thermal_stress, axisymmetric

Flange welded on a pipe with internal fluid at 0 C (h = 5000 W/m2C) and external steam at 100 C (h = 20000 W/m2C), k = 20 W/mC. The book finds a minimum T of 3.59 C on the bore, maximum 99.99 C, radial flux 170000 W/m2 near AH, and in Table 8.6-1 the maximum and minimum stresses (sigma_r, sigma_theta, sigma_z, von Mises) for the two support conditions.

**Model**: Axisymmetric mesh of 106 Q8 rebuilt from Fig. 8.6-1 (pipe r 77-84, hub r 84-102 48 wide, ring r 102-156 39 wide, 18 mm outer weld, inner welded stretch up to z = 18); separate nodes along IJ (18 < z < 48) for the non-conducting, non-welded interface. Thermal field with FieldModel('heat') (axisym Q8, convection with add_convection), then the same mesh and ids in a Model of 'axisym' plane elements loaded with FieldResult.to_model_temperatures; two analyses: only B axially restrained, or the whole of BC.

![cookmod-8.6-pipe-flange-thermal-stress](images/books/cookmod-8.6-pipe-flange-thermal-stress.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| minimum temperature (bore, AB) [C] | book | 3.59 | 3.5468 | 1.20 % | ✅ |
| maximum temperature (outside) [C] | book | 99.99 | 99.99 | 0.00 % | ✅ |
| radial flux near AH [W/m2] | book | 1.7e+05 | 1.6837e+05 | 0.96 % | ✅ |
| limiting flux k DeltaT / Delta r (Eq. 8.6-1) [W/m2] | book | 2.86e+05 | 2.8571e+05 | 0.10 % | ✅ |
| estimate alpha E DeltaT (100 C) [MPa] | book | 240 | 240 | 0.00 % | ✅ |
| estimate alpha E DeltaT (30 C) [MPa] | book | 72 | 72 | 0.00 % | ✅ |
| maximum bore sigma_theta, only B restrained [MPa] | book | 216 | 217.13 | 0.53 % | ✅ |
| maximum sigma_r, only B restrained (corners) [MPa] | book | 113 | 101.18 | 10.46 % | ✅ |
| minimum sigma_r, only B restrained (corner G) [MPa] | book | -68 | -60.941 | 10.38 % | ✅ |
| minimum sigma_theta, only B restrained (corner G) [MPa] | book | -142 | -134.99 | 4.94 % | ✅ |
| maximum sigma_z, only B restrained (corners) [MPa] | book | 244 | 185.22 | 24.09 % | ✅ |
| minimum sigma_z, only B restrained (corner G) [MPa] | book | -199 | -172.92 | 13.11 % | ✅ |
| maximum von Mises, only B restrained (corners) [MPa] | book | 222 | 192.16 | 13.44 % | ✅ |
| maximum bore sigma_theta, all of BC restrained [MPa] | book | 229 | 220.49 | 3.72 % | ✅ |
| maximum sigma_r, all of BC restrained (corners) [MPa] | book | 105 | 86.165 | 17.94 % | ✅ |
| minimum sigma_r, all of BC restrained (corner G) [MPa] | book | -107 | -97.469 | 8.91 % | ✅ |
| minimum sigma_theta, all of BC restrained (corner G) [MPa] | book | -225 | -221.32 | 1.64 % | ✅ |
| maximum sigma_z, all of BC restrained (corners) [MPa] | book | 257 | 247.36 | 3.75 % | ✅ |
| minimum sigma_z, all of BC restrained (corner G) [MPa] | book | -279 | -254.2 | 8.89 % | ✅ |
| maximum von Mises, all of BC restrained (corners) [MPa] | book | 242 | 218.47 | 9.72 % | ✅ |

**Notes**: Flange and weld geometry only partly dimensioned: positions of I and J and the inner weld rebuilt from the drawing. The thermal field matches the book (minimum T 3.55 C against 3.59; flux 168000 W/m2). The maximum bore sigma_theta (216 and 229 MPa in the book) is reproduced within 4 %. The other table extremes fall at the re-entrant corners G, I, J, where the stress is theoretically infinite (the book itself calls the contours 'a mess'): they depend on the mesh (doubling the elements, the maximum sigma_r goes from 101 to 152 MPa) and are compared with a 25 % tolerance. The book estimates alpha E DeltaT = 240 and 72 MPa are verified.

*Code*: `validation/books/cookmodeling/ch08.py`

## Chapter 9

<a id="cookmod-9-3-mass-matrices"></a>

### Sez. 9.3, Eqq. 9.3-1...9.3-5 - Lumped and consistent mass matrices of bar and beam

📐 · PDF page 240 · dynamics

Bar: lumped mass diag(rho A L / 2) and consistent rho A L [[1/3, 1/6], [1/6, 1/3]] for linear lateral displacements. Plane beam: consistent matrix rho A L / 420 [[156, 22L, 54, -13L], ...]. Data chosen: rho = 7.85, A = 2, L = 3.

**Model**: feagent matrices: L2 bar (consistent_mass and lumped_masses of add_bar) and beam (BeamElement3D.consistent_mass_local, bending block v1, theta1, v2, theta2), compared with the book formulas.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| bar: lumped m11 = rho A L / 2 | exact | 23.55 | 23.55 | 0.00 % | ✅ |
| consistent bar: m11 = rho A L / 3 | exact | 15.7 | 15.7 | 0.00 % | ✅ |
| consistent bar: m12 = rho A L / 6 | exact | 7.85 | 7.85 | 0.00 % | ✅ |
| consistent beam: maximum difference from Eq. 9.3-5 | exact | 0 | 3.5527e-15 | 0.00 % | ✅ |
| consistent beam: m22 = 4 rho A L^3 / 420 | exact | 4.0371 | 4.0371 | 0.00 % | ✅ |

**Notes**: All terms match; the book's 'arbitrary' rotational mass (rho A L^3 / 24) is not used by feagent, whose lumped beam mass carries no rotary inertia.

*Code*: `validation/books/cookmodeling/ch09.py`

<a id="cookmod-9-4-cantilever-one-element-modal"></a>

### Sez. 9.4 Example, Fig. 9.4-1, Eqq. 9.4-4...9.4-7 - Frequencies of a cantilever with a single beam element: consistent and lumped mass

✅ · PDF page 243 · dynamics, beam_2d

With consistent mass omega1 = 3.533 and omega2 = 34.81 sqrt(EI / (rho A L^4)), modes theta2 = 1.38/L and 7.62/L; with lumped mass omega1 = 2.449, theta2 = 1.50/L. Exact 3.516 and 22.03.

**Model**: One clamped Euler-Bernoulli beam (E I = 1, rho A = 1, L = 1) with only v2 and theta2; Model.modal with mass='consistent' and with mass='lumped' (mass rho A L / 2 on the translation, rotation condensed).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| omega1 consistent | book | 3.533 | 3.5327 | 0.01 % | ✅ |
| omega2 consistent | book | 34.81 | 34.807 | 0.01 % | ✅ |
| mode 1 theta2/v2 [1/L] | book | 1.38 | 1.3775 | 0.18 % | ✅ |
| mode 2 theta2/v2 [1/L] | book | 7.62 | 7.6225 | 0.03 % | ✅ |
| omega1 lumped | book | 2.449 | 2.4495 | 0.02 % | ✅ |
| mode lumped theta2/v2 [1/L] | book | 1.5 | 1.5 | 0.00 % | ✅ |

*Code*: `validation/books/cookmodeling/ch09.py`

<a id="cookmod-9-6-guyan-one-element"></a>

### Sez. 9.6 Example, Eqq. 9.6-6...9.6-8 - Guyan reduction of the one-element cantilever

✅ · PDF page 246 · dynamics, reduction

With v2 as master the slave rotation is theta2 = 1.5 v2 / L, the reduced stiffness is 3 E I / L^3 and the frequency 3.568 sqrt(EI / (rho A L^4)), higher than the 3.533 of the full system because reduction acts as a constraint.

**Model**: One-element cantilever of Sec. 9.4 reduced with feagent.reduction.guyan to the single master v2 (consistent mass); transformation T from the reduction matrix.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| transformation theta2 = 1.5 v2 / L [1/L] | exact | 1.5 | 1.5 | 0.00 % | ✅ |
| reduced stiffness 3 E I / L^3 [EI/L^3] | exact | 3 | 3 | 0.00 % | ✅ |
| frequency of the reduced system | book | 3.568 | 3.5675 | 0.01 % | ✅ |

*Code*: `validation/books/cookmodeling/ch09.py`

<a id="cookmod-9-11-frequency-estimates"></a>

### Sez. 9.11, Eqq. 9.11-1, 9.11-2 - First-frequency estimate with static stiffness and effective mass m/2

✅ · PDF page 260 · dynamics

Quick estimate omega = sqrt(k / (m/2)): simply supported beam 9.80 sqrt(EI / (m L^3)) against exact 9.87; cantilever 2.45, 30 % below the exact 3.52.

**Model**: Stiffness k = P / Delta computed by feagent with a static force (simply supported beam at midspan, cantilever at the tip; 40 beams), effective mass m/2; exact frequencies from the consistent modal analysis of the same mesh.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| simply supported: estimate sqrt(k / (m/2)) | book | 9.8 | 9.798 | 0.02 % | ✅ |
| simply supported: frequency from modal analysis | book | 9.87 | 9.8696 | 0.00 % | ✅ |
| cantilever: estimate sqrt(k / (m/2)) | book | 2.45 | 2.4495 | 0.02 % | ✅ |
| cantilever: frequency from modal analysis | book | 3.516 | 3.516 | 0.00 % | ✅ |

*Code*: `validation/books/cookmodeling/ch09.py`

<a id="cookmod-9-11-guyan-cantilever-plate"></a>

### Sez. 9.11, Fig. 9.11-4 - Square cantilever plate: full frequencies and Guyan-reduced with 6 masters

🟡 · PDF page 261 · dynamics, plate, reduction

Thin square plate clamped on one edge: with 90 dof the first four frequencies are 3.469; 8.535; 21.45; 27.059; reduced to 6 masters 3.473; 8.604; 22.69; 29.49. Reduction raises mostly the higher frequencies.

**Model**: 5 x 5 mesh of squares split by the diagonal as in the figure (50 'thin' DKT triangles, 90 free dof), one edge clamped, consistent mass; Guyan reduction to the 6 lateral translations marked in the figure (three nodes on the free edge, three at 3/5 of the span). Dimensionless frequencies omega sqrt(rho t L^4 / D).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| omega1, full system (different element) | book | 3.469 | 3.4612 | 0.22 % | ✅ |
| omega1: reduced / full ratio | book | 1.0012 | 1.001 | 0.01 % | ✅ |
| omega2, full system (different element) | book | 8.535 | 8.5888 | 0.63 % | ✅ |
| omega2: reduced / full ratio | book | 1.0081 | 1.0085 | 0.04 % | ✅ |
| omega3, full system (different element) | book | 21.45 | 22.068 | 2.88 % | ✅ |
| omega3: reduced / full ratio | book | 1.0578 | 1.0561 | 0.16 % | ✅ |
| omega4, full system (different element) | book | 27.059 | 28.251 | 4.40 % | ✅ |
| omega4: reduced / full ratio | book | 1.0898 | 1.0926 | 0.25 % | ✅ |

**Notes**: The book's plate element (ref. [9.7]) is not specified: with feagent's DKT the full frequencies differ by 0.2; 0.6; 2.9; 4.4 %. The increases due to Guyan reduction match the book within 0.3 % instead (1.0010; 1.0085; 1.056; 1.093 against 1.0012; 1.0081; 1.058; 1.090).

*Code*: `validation/books/cookmodeling/ch09.py`

<a id="cookmod-9-11-spin-softening"></a>

### Sez. 9.11, Fig. 9.11-6, Eqq. 9.11-3...9.11-5 - Spin softening of a mass-spring system on a rotating disk

📐 · PDF page 264 · dynamics

A rigid bar on a disk spinning at Omega, with masses and springs at its ends, oscillates at omega = (k/m - Omega^2)^(1/2): rotation lowers the frequency ('spin softening'). Data chosen: k = 100, m = 1, a = 0.1, R = 1.

**Model**: Rigid bar ABC (very stiff beam) pivoted at B, masses m at A and C (add_nodal_mass), radial springs k (add_elastic_support); feagent has no centrifugal stiffness, which is added as a superelement of stiffness -m Omega^2 on the radial direction of A and C (linearized Eq. 9.11-3).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Omega = 0: omega = (k/m - Omega^2)^(1/2) [rad/s] | exact | 10 | 10 | 0.00 % | ✅ |
| Omega = 4: omega = (k/m - Omega^2)^(1/2) [rad/s] | exact | 9.1652 | 9.1652 | 0.00 % | ✅ |
| Omega = 8: omega = (k/m - Omega^2)^(1/2) [rad/s] | exact | 6 | 6 | 0.00 % | ✅ |

**Notes**: Check of the book formula with feagent's modal analysis; the softening term is inserted by hand because feagent does not compute the effects of the transport inertia forces.

*Code*: `validation/books/cookmodeling/ch09.py`

<a id="cookmod-9-12-frame-modes-harmonic"></a>

### Sez. 9.12 An Application, Figg. 9.12-1...9.12-3, Tab. 9.12-1 - Right-angle frame: natural modes and harmonic response

✅ · PDF page 268 · dynamics, beam_2d

Steel frame of 0.1 m square section, 3 m column pinned at A and 2 m beam on a roller at C. The book estimates f1 = 3.20 Hz (k = 111100 N/m, effective mass 275 kg), computes the first ten frequencies (3.315 ... 649.6 Hz), the components of the mass-normalized modes and the response to 3000 sin(Omega t) N at C: at resonance with mode 4 u16 = 75.5e-6 m.

**Model**: 50 Timoshenko beams 0.1 m long (30 on column AB, 20 on beam BC), lumped mass (Model.modal mass='lumped'); harmonic response by superposition of the first 5 modes with 2 % modal damping, computed with feagent's normalized modes.

![cookmod-9.12-frame-modes-harmonic](images/books/cookmod-9.12-frame-modes-harmonic.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| frequency 1 [Hz] | book | 3.315 | 3.3056 | 0.28 % | ✅ |
| frequency 2 [Hz] | book | 35.08 | 34.993 | 0.25 % | ✅ |
| frequency 3 [Hz] | book | 70.77 | 70.602 | 0.24 % | ✅ |
| frequency 4 [Hz] | book | 122.7 | 122.46 | 0.20 % | ✅ |
| frequency 5 [Hz] | book | 226 | 225.61 | 0.17 % | ✅ |
| frequency 6 [Hz] | book | 269.4 | 268.94 | 0.17 % | ✅ |
| frequency 7 [Hz] | book | 396.6 | 395.73 | 0.22 % | ✅ |
| frequency 8 [Hz] | book | 420.8 | 420.52 | 0.07 % | ✅ |
| frequency 9 [Hz] | book | 552.3 | 552.14 | 0.03 % | ✅ |
| frequency 10 [Hz] | book | 649.6 | 649.73 | 0.02 % | ✅ |
| 1000 |u| node 16, mode 1 | book | 38.6 | 38.633 | 0.09 % | ✅ |
| 1000 |u| node 16, mode 2 | book | 81.5 | 81.625 | 0.15 % | ✅ |
| 1000 |u| node 16, mode 3 | book | 23.8 | 23.872 | 0.30 % | ✅ |
| 1000 |u| node 16, mode 4 | book | 27.7 | 27.829 | 0.46 % | ✅ |
| 1000 |u| node 16, mode 5 | book | 62.7 | 63.11 | 0.65 % | ✅ |
| 1000 |theta| node 41, mode 1 | book | 1 | 0.98464 | 1.54 % | ✅ |
| 1000 |theta| node 41, mode 2 | book | 7 | 6.9958 | 0.06 % | ✅ |
| 1000 |theta| node 41, mode 3 | book | 19.4 | 19.426 | 0.13 % | ✅ |
| 1000 |theta| node 41, mode 4 | book | 52.6 | 52.715 | 0.22 % | ✅ |
| 1000 |theta| node 41, mode 5 | book | 248.9 | 252.76 | 1.55 % | ✅ |
| 1000 |u| node 51, mode 1 | book | 62.3 | 62.281 | 0.03 % | ✅ |
| 1000 |u| node 51, mode 2 | book | 33.1 | 33.101 | 0.00 % | ✅ |
| 1000 |u| node 51, mode 3 | book | 3.4 | 3.3711 | 0.85 % | ✅ |
| 1000 |u| node 51, mode 4 | book | 21.6 | 21.647 | 0.22 % | ✅ |
| 1000 |u| node 51, mode 5 | book | 9.2 | 9.2279 | 0.30 % | ✅ |
| horizontal stiffness at C (Eq. 9.12-1) [N/m] | book | 1.111e+05 | 1.1091e+05 | 0.17 % | ✅ |
| f1 estimated with 275 kg effective mass [Hz] | book | 3.2 | 3.1963 | 0.12 % | ✅ |
| modal load of mode 4 [N] | book | 64.8 | 64.952 | 0.24 % | ✅ |
| maximum modal amplitude of mode 4 (Eq. 9.12-3) | book | 0.00273 | 0.0027427 | 0.47 % | ✅ |
| u16 amplitude with Omega = omega4 [m] | book | 7.5500e-05 | 7.5930e-05 | 0.57 % | ✅ |
| horizontal reaction at A with Omega = omega4 (about) [N] | book | 4800 | 4686.1 | 2.37 % | ✅ |

**Notes**: The frequencies match within 0.3 % using Timoshenko beams (the book gives G) and lumped mass (with Euler-Bernoulli beams mode 10 rises to 677 Hz). The book warns that the data come from three different programs: the modal components of Table 9.12-1 are reproduced with Euler-Bernoulli beams and consistent mass (with Timoshenko theta41 of modes 3 and 4 drops by 3 %, a sign that the table comes from a program without shear deformation); comparison in absolute value (the sign of a mode is arbitrary) within 2 %. The horizontal reaction at A at resonance with mode 4 is 4.8 kN, 1.6 times the force, as in the book.

*Code*: `validation/books/cookmodeling/ch09.py`

<a id="cookmod-9-13-frame-impulse-response"></a>

### Sez. 9.13 An Application, Figg. 9.13-1...9.13-3, Tabb. 9.13-1, 9.13-2 - Frame under a rectangular pulse: modal superposition and response spectrum

🟡 · PDF page 271 · dynamics

A 100 kN horizontal force acts at C for 0.01 s. The book estimates a = 364 m/s2, v = 3.64 m/s, u = 0.181 m and tabulates the maxima of u16, its velocity and acceleration and of theta41 and derivatives as the number of modes grows (Table 9.13-1), then the modal contributions of the response spectrum (Table 9.13-2).

**Model**: Frame of Sec. 9.12; Model.solve_time_history_modal with 1, 2, 3, 4, 5, 10, 15 modes, exact piecewise integration, dt = 1e-4 s up to 0.4 s, 2 % damping; response spectrum of the rectangular pulse computed with feagent's modes and SRSS combination.

![cookmod-9.13-frame-impulse-response](images/books/cookmod-9.13-frame-impulse-response.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| maximum 1e3 u16, 1 modes [m] | book | 112 | 112.65 | 0.58 % | ✅ |
| maximum v16, 1 modes [m/s] | book | 2.38 | 2.3899 | 0.42 % | ✅ |
| maximum a16, 1 modes [m/s2] | book | 242 | 240.57 | 0.59 % | ✅ |
| maximum 1e3 theta41, 1 modes [rad] | book | 2.8 | 2.8235 | 0.84 % | ✅ |
| maximum thetadot41, 1 modes [rad/s] | book | 0.06 | 0.059902 | 0.16 % | ✅ |
| maximum thetaddot41, 1 modes [rad/s2] | book | 6 | 6.0299 | 0.50 % | ✅ |
| maximum 1e3 u16, 2 modes [m] | book | 119 | 119.4 | 0.34 % | ✅ |
| maximum v16, 2 modes [m/s] | book | 4.33 | 4.3473 | 0.40 % | ✅ |
| maximum a16, 2 modes [m/s2] | book | 463 | 462.8 | 0.04 % | ✅ |
| maximum 1e3 theta41, 2 modes [rad] | book | 3.37 | 3.3925 | 0.67 % | ✅ |
| maximum thetadot41, 2 modes [rad/s] | book | 0.211 | 0.21184 | 0.40 % | ✅ |
| maximum thetaddot41, 2 modes [rad/s2] | book | 40 | 39.58 | 1.05 % | ✅ |
| maximum a16, 3 modes [m/s2] | book | 464 | 463.18 | 0.18 % | ✅ |
| maximum thetaddot41, 3 modes [rad/s2] | book | 44 | 44.22 | 0.50 % | ✅ |
| maximum a16, 4 modes [m/s2] | book | 529 | 530.5 | 0.28 % | ✅ |
| maximum thetaddot41, 4 modes [rad/s2] | book | 174 | 177.44 | 1.98 % | ✅ |
| maximum 1e3 u16, 5 modes [m] | book | 119 | 119.38 | 0.32 % | ✅ |
| maximum v16, 5 modes [m/s] | book | 4.31 | 4.3283 | 0.42 % | ✅ |
| maximum a16, 5 modes [m/s2] | book | 553 | 556.63 | 0.66 % | ✅ |
| maximum 1e3 theta41, 5 modes [rad] | book | 3.47 | 3.4917 | 0.63 % | ✅ |
| maximum thetadot41, 5 modes [rad/s] | book | 0.368 | 0.37468 | 1.82 % | ✅ |
| maximum thetaddot41, 5 modes [rad/s2] | book | 441 | 460.37 | 4.39 % | ✅ |
| maximum a16, 10 modes [m/s2] | book | 830 | 829.57 | 0.05 % | ✅ |
| maximum thetaddot41, 10 modes [rad/s2] | book | 862 | 872.91 | 1.27 % | ✅ |
| maximum 1e3 u16, 15 modes [m] | book | 119 | 119.38 | 0.32 % | ✅ |
| maximum v16, 15 modes [m/s] | book | 4.39 | 4.405 | 0.34 % | ✅ |
| maximum a16, 15 modes [m/s2] | book | 1341 | 1360.4 | 1.45 % | ✅ |
| maximum 1e3 theta41, 15 modes [rad] | book | 3.47 | 3.4937 | 0.68 % | ✅ |
| maximum thetadot41, 15 modes [rad/s] | book | 0.603 | 0.60351 | 0.08 % | ✅ |
| maximum thetaddot41, 15 modes [rad/s2] | book | 1524 | 1628.2 | 6.84 % | ✅ |
| maximum displacement at C, 15 modes [m] | book | 0.181 | 0.18194 | 0.52 % | ✅ |
| maximum velocity at C, 15 modes [m/s] | book | 4.27 | 4.275 | 0.12 % | ✅ |
| maximum acceleration at C, 15 modes (printed 173) [m/s2] | corrected | 1730 | 1728.4 | 0.09 % | ✅ |
| estimate u = v sqrt(m/k) (Eq. 9.13-3b) [m] | book | 0.181 | 0.18092 | 0.05 % | ✅ |
| spectrum: mode 1, u16 [1e-3 m] | book | 115 | 115.62 | 0.54 % | ✅ |
| spectrum: mode 2, velocity of 16 [m/s] | book | 2.17 | 2.1894 | 0.90 % | ✅ |
| SRSS spectrum 1-15: u16 [1e-3 m] | book | 116 | 116.05 | 0.04 % | ✅ |
| SRSS spectrum 1-15: velocity of 16 [m/s] | book | 3.25 | 3.2613 | 0.35 % | ✅ |
| SRSS spectrum 1-15: acceleration of 16 [m/s2] | book | 719 | 753.37 | 4.78 % | ✅ |
| SRSS spectrum 1-15: theta41 [1e-3 rad] | book | 3.09 | 3.0569 | 1.07 % | ✅ |

**Notes**: Displacements and velocities match within 2 %; accelerations, dominated by the high modes (lumped mass with rotary inertia undocumented in the book), differ by up to 7 % for theta41 with 15 modes: 8 % tolerance on accelerations. Book misprint: the maximum acceleration of node 51 with 15 modes is printed as 173 m/s2, but the table already gives 1341 m/s2 at node 16; feagent gives 1728 m/s2, so the correct value is about 1730 (a digit is missing). Spectrum: the first row of Table 9.13-2 does not match Table 9.13-1 even in the book (undamped spectrum); contributions and SRSS combinations are compared within 5 %.

*Code*: `validation/books/cookmodeling/ch09.py`

## Chapter 10

<a id="cookmod-10-2-nonlinear-spring-algorithms"></a>

### Sez. 10.2, Figg. 10.2-1...10.2-3 (Problems 10.2, 10.3) - Nonlinear spring: incremental, Newton-Raphson and modified Newton-Raphson methods

✅ · PDF page 287 · nonlinear

Problem 10.2: softening spring k = 5 / (2 + u), hence P = 5u / (2 + u), with P = 2 and 4.5 (exact solutions u = 4/3 and 18). Problem 10.3: hardening spring k = 2 + u^2, P = 2u + u^3. The book describes the drift of the purely incremental method and the convergence of NR and modified NR, without printed answers.

**Model**: Two-node spring with a nonlinear force-stretch law (feagent.inelastic.NonlinearElastic, fine table) solved with solve_inelastic (incremental Newton-Raphson); the book's purely incremental and modified Newton-Raphson methods are repeated with numpy on the same law to show their behaviour.

![cookmod-10.2-nonlinear-spring-algorithms](images/books/cookmod-10.2-nonlinear-spring-algorithms.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| softening spring, P = 2.0: u (feagent Newton-Raphson) | exact | 1.3333 | 1.3333 | 0.00 % | ✅ |
| softening spring, P = 4.5: u (feagent Newton-Raphson) | exact | 18 | 18 | 0.00 % | ✅ |
| incremental method, P = 2 (drift) | exact | 0.8 | 0.8 | 0.00 % | ✅ |
| incremental method, P = 4.5 (drift) | exact | 2.76 | 2.76 | 0.00 % | ✅ |
| two Newton-Raphson iterations at P = 2 | exact | 1.3333 | 1.3311 | 0.16 % | ✅ |
| modified NR farther from the solution after two iterations (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| hardening spring k = 2 + u^2, P = 2 | exact | 0.77092 | 0.77092 | 0.00 % | ✅ |

**Notes**: References: exact solutions of the problems. The two-step incremental method (P = 2 and 4.5) gives u = 0.80 and 2.76 against 1.33 and 18: the book's drift. Two NR iterations at P = 2 reach 1.3312 (error 0.2 %), modified NR stops farther away. On the hardening spring NR converges to u = 0.7709 for P = 2.

*Code*: `validation/books/cookmodeling/ch10.py`

<a id="cookmod-10-3-rigid-bar-stress-stiffness"></a>

### Sez. 10.3, Fig. 10.3-1a,b, Eq. 10.3-1 - Rigid bar on a spring with axial force: geometric stiffness P/L

📐 · PDF page 291 · buckling, geometric_stiffness

For a rigid bar of length L on a spring k loaded by axial P and lateral F: (k + P/L) v2 = F; tension stiffens, compression softens and P = -k L is the critical load. Data chosen: L = 2, k = 10, F = 1, P = +-5.

**Model**: Very stiff beam pinned at the left, lateral spring k at the right end (add_elastic_support); P-Delta analysis (solve_pdelta) with axial force P and lateral force F, linear buckling (Model.buckling) with compressive P.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| P = 5: v2 = F / (k + P/L) | exact | 0.08 | 0.08 | 0.00 % | ✅ |
| P = -5: v2 = F / (k + P/L) | exact | 0.13333 | 0.13333 | 0.00 % | ✅ |
| critical load -P = k L | exact | 20 | 20 | 0.00 % | ✅ |

*Code*: `validation/books/cookmodeling/ch10.py`

<a id="cookmod-10-3-one-element-buckling"></a>

### Sez. 10.3, Eqq. 10.3-3...10.3-5 (Problem 10.4) - Critical load of a cantilever with a single beam element

✅ · PDF page 292 · buckling

With the consistent geometric matrix P/(30L) [[36, 3L, ...]] a single element gives P_cr = 2.486 E I / L^2 against 2.467 (pi^2/4) from theory. Problem 10.4 asks for the other cases without printed answers.

**Model**: One clamped Euler-Bernoulli beam (E I = 1, L = 1) with consistent geometric stiffness, Model.buckling with unit axial force; for Problem 10.4: simply supported with one element, clamped-clamped with two, clamped-pinned with one.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| cantilever, one element [EI/L^2] | book | 2.486 | 2.486 | 0.00 % | ✅ |
| cantilever, theory pi^2/4 [EI/L^2] | book | 2.467 | 2.4674 | 0.02 % | ✅ |
| simply supported, one element (Problem 10.4a) [EI/L^2] | exact | 12 | 12 | 0.00 % | ✅ |
| clamped-clamped, two elements (Problem 10.4b) [EI/L^2] | exact | 40 | 40 | 0.00 % | ✅ |
| clamped-pinned, one element (Problem 10.4c) [EI/L^2] | exact | 30 | 30 | 0.00 % | ✅ |

**Notes**: For Problem 10.4 the one-element values follow from the same matrix: simply supported 12 (against pi^2 = 9.87), clamped-clamped with two elements 40 (against 4 pi^2 = 39.5), clamped-pinned 30 (against 20.19); compared with the solution of the 2x2 or 1x1 determinant of the book's matrix.

*Code*: `validation/books/cookmodeling/ch10.py`

<a id="cookmod-10-4-beam-on-wall-snap"></a>

### Sez. 10.4, Fig. 10.4-1, Eqq. 10.4-1...10.4-3 (Problem 10.5) - Inclined bar with a roller on a wall: displacement-dependent stiffness, limit point and snap-through

✅ · PDF page 293 · geometric_nonlinear

For c << L: F = [AE/(2L^3) (2c - v)(c - v) + k] v, tangent stiffness AE/(2L^3)(2c^2 - 6cv + 3v^2) + k, compression P = AE (2c - v) v / (2 L^2). With k = 0 there is a limit point at v = (1 - sqrt(3)/3) c and a snap-through to v = 2c; with k > AE c^2 / (2 L^3) the tangent stiffness stays positive.

**Model**: Corotational L2 bar (feagent.corotational.solve_corotational) between the hinge and the roller on the wall, linear spring k (add_elastic_support); with k = 0 arc-length control to follow the snap-through, with large k displacement control. Data chosen: L = 100, c = 2, EA = 1e4.

![cookmod-10.4-beam-on-wall-snap](images/books/cookmod-10.4-beam-on-wall-snap.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| k = 0: force at the limit point (Eq. 10.4-1) | book | 0.015396 | 0.015389 | 0.05 % | ✅ |
| k = 0: limit-point displacement (1 - sqrt(3)/3) c (arc step) | book | 0.8453 | 0.8521 | 0.80 % | ✅ |
| k = 0: zero F at v = 2c (snap-through traversed) | book | 0 | 2.2839e-07 | 0.00 % | ✅ |
| k = 0: F for v = 0.5 versus Eq. 10.4-1 | book | 0.013125 | 0.013118 | 0.06 % | ✅ |
| k = 0: F for v = 1.0 versus Eq. 10.4-1 | book | 0.015 | 0.014994 | 0.04 % | ✅ |
| k = 0: F for v = 3.0 versus Eq. 10.4-1 | book | -0.015 | -0.014994 | 0.04 % | ✅ |
| k > AE c^2 / (2 L^3): F increasing with v (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| large k: F for v = c | book | 0.08 | 0.080001 | 0.00 % | ✅ |

**Notes**: The corotational path is the exact one of the bar (stretch computed without approximations); the book's formulas are its shallow form and agree within (c/L)^2 = 4e-4. With k = 0 the arc-length passes the limit point and returns to F = 0 at v = 2c (flipped configuration).

*Code*: `validation/books/cookmodeling/ch10.py`

<a id="cookmod-10-4-geometric-nonlinear-tests"></a>

### Sez. 10.4, Fig. 10.4-2a,b,e - Geometric nonlinearity test cases: rigid bar with rotational spring, cantilever with end moment, membrane

🟡 · PDF page 295 · geometric_nonlinear

(a) Rigid bar on a rotational spring: linear critical load k_theta / L, at large rotations P = k_theta theta / (L sin theta). (b) Cantilever with end moment: u = (1 - cos phi) L / phi, phi = M L / E I. (e) Square membrane under pressure: w_c = c t [(q/E)(b/t)^4]^(1/3), c = 0.2866.

**Model**: Corotational analysis (solve_corotational): (a) very stiff vertical beam with a rotational spring k_theta at the base (add_elastic_support rz) and vertical load, tilted by 0.001 rad to trigger the branch, control of the top horizontal displacement (P = k_theta (theta - theta0) / (L sin theta)); (b) cantilever of 20 beams with an end moment up to M L / E I = 2 pi (closing into a ring).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| (a) linear critical load k_theta / L | exact | 10 | 10 | 0.00 % | ✅ |
| (a) P at theta = 11.6 degrees | exact | 10.019 | 10.019 | 0.00 % | ✅ |
| (a) P at theta = 23.6 degrees | exact | 10.265 | 10.265 | 0.00 % | ✅ |
| (a) P at theta = 53.2 degrees | exact | 11.585 | 11.585 | 0.00 % | ✅ |
| (b) lateral displacement for phi = 1.57 | exact | 0.63662 | 0.63678 | 0.03 % | ✅ |
| (b) lateral displacement for phi = 3.14 | exact | 0.63662 | 0.63727 | 0.10 % | ✅ |
| (b) lateral displacement for phi = 6.28 | exact | 0 | -3.1745e-13 | 0.00 % | ✅ |
| (b) phi = 2 pi: the tip returns to the clamp (u_x = -L) | exact | 0 | 3.8544e-12 | 0.00 % | ✅ |

**Notes**: Cases (a) and (b) redone; the membrane (e) needs large-displacement shells, not yet available in feagent (the corotational analysis covers plane frames): not redone. With 20 straight elements the cantilever closing into a ring carries the chord error (about 0.4 % on the displacement at phi = pi).

*Code*: `validation/books/cookmodeling/ch10.py`

<a id="cookmod-10-5-plastic-block-sampling"></a>

### Sez. 10.5, Figg. 10.5-2, 10.5-3 - Plasticity: Gauss points as yield samplers in a bent block

✅ · PDF page 297 · plasticity, plane_stress

Yielding is detected only when it reaches the sampling points: with a single element through the depth the Gauss points lie h / (2 sqrt 3) from the axis and the block stays elastic up to a curvature sqrt(3) times the first-yield one; more elements are needed for a good moment-curvature relation. The book gives no numbers.

**Model**: 1 x 1 plane-stress block (nu = 0) of 2x2-Gauss-point Q8 with 1, 2 and 8 elements through the depth, bilinear plasticity (feagent.inelastic.J2Plasticity, E = 1000, sigma_y = 1, E_t = 100), curvature imposed by rotating the free face (add_settlement) and solve_inelastic; moment from the reactions.

![cookmod-10.5-plastic-block-sampling](images/books/cookmod-10.5-plastic-block-sampling.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| 1 element: no yield at 1.7 kappa_y, yield at 1.8 (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| 1 element: moment at 1.5 kappa_y equal to the elastic one | exact | 0.25 | 0.25 | 0.00 % | ✅ |
| 8 elements: yield already at 1.5 kappa_y (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| 8 elements: M at 0.5 kappa_y versus the exact section | exact | 0.083333 | 0.083333 | 0.00 % | ✅ |
| 8 elements: M at 1.5 kappa_y versus the exact section | exact | 0.21667 | 0.21657 | 0.05 % | ✅ |
| 8 elements: M at 1.7 kappa_y versus the exact section | exact | 0.22738 | 0.22709 | 0.13 % | ✅ |
| 8 elements: M at 1.8 kappa_y versus the exact section | exact | 0.23185 | 0.23235 | 0.21 % | ✅ |
| 8 elements: M at 3 kappa_y versus the exact section | exact | 0.26667 | 0.26641 | 0.10 % | ✅ |
| 8 elements: M at 6 kappa_y versus the exact section | exact | 0.32292 | 0.32297 | 0.02 % | ✅ |

**Notes**: Reference: exact moment-curvature relation of the bilinear section. With one element yielding appears between 1.7 and 1.8 times the first-yield curvature (sqrt 3 = 1.73) and the moment at 1.5 times stays elastic (+8 % over exact); with 8 elements the curve is within 1 %.

*Code*: `validation/books/cookmodeling/ch10.py`

<a id="cookmod-10-6-gap-two-springs"></a>

### Sez. 10.6, Figg. 10.6-2, 10.6-3 (Problem 10.12) - Two springs in series with a gap g to a wall: penalty gap element

✅ · PDF page 300 · contact

Gap g closes when u_B > g; the stiff contact spring k3 then applies the wall reaction. Problem 10.12: k1 = k2 = 1 N/mm, k3 = 100 N/mm, g = 1 mm, P = 3 N, without printed answer.

**Model**: Linear springs k1 (wall-A) and k2 (A-B), spring k3 between B and the wall with feagent.inelastic.Gap (gap g, compression only); solve_inelastic.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u_A at P = 3 N [mm] | exact | 2.005 | 2.005 | 0.00 % | ✅ |
| u_B at P = 3 N [mm] | exact | 1.01 | 1.01 | 0.00 % | ✅ |
| gap force [N] | exact | 0.99502 | 0.99502 | 0.00 % | ✅ |
| P = 1 N: u_B = u_A (gap open) [mm] | exact | 0 | 0 | 0.00 % | ✅ |

**Notes**: Exact solution: u_A = 2.00498 mm, u_B = 1.00995 mm (the contact spring shortens by 0.0099 mm), contact force 0.995 N; before closure u_B = u_A (closure occurs at P = 1 N).

*Code*: `validation/books/cookmodeling/ch10.py`

<a id="cookmod-10-8-frame-geometric-nonlinearity"></a>

### Sez. 10.8 Applications, Figg. 10.8-1, 10.8-2 - Slender frame: linear buckling and large-displacement response with snap

✅ · PDF page 305 · buckling, geometric_nonlinear

Steel frame ABCD with 8 x 8 mm section loaded by horizontal P at C. Hand estimate 146 N; linear buckling gives 137 N (114 N with the imperfection, D moving left). The nonlinear analysis of the perfect frame shows no instability; with the imperfection D first moves right, then reverses and the frame snaps at P = 86 N.

**Model**: 19 Euler-Bernoulli beams (5 on AB, 2 on BC, 12 on CD); linear buckling with Model.buckling; large-displacement response with feagent.corotational.solve_corotational in arc length; imperfection: last element of CD inclined with D moved 2 mm to the left.

![cookmod-10.8-frame-geometric-nonlinearity](images/books/cookmod-10.8-frame-geometric-nonlinearity.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| linear buckling, perfect frame [N] | book | 137 | 137.18 | 0.13 % | ✅ |
| linear buckling, with imperfection [N] | book | 114 | 114.48 | 0.42 % | ✅ |
| mode with imperfection: D moves opposite to C (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| estimate 5 P_cr = pi^2 E I / (4 L^2) [N] | book | 146 | 146.22 | 0.15 % | ✅ |
| snap load (limit point), with imperfection [N] | book | 86 | 86.396 | 0.46 % | ✅ |
| D first moves right and then reverses (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| perfect frame: stable path up to 150 N (1 = true) | book | 1 | 1 | 0.00 % | ✅ |

**Notes**: Linear buckling and limit point match the book. At P = 150 N the perfect frame has u_C = 6.1 mm and u_D = 13.0 mm (about 7 and 12 from the book's plot, not compared). Remark on feagent: with the default tolerance (1e-9) the arc length stops at the first step on this frame, while load control converges; with tol = 1e-7 the path is complete.

*Code*: `validation/books/cookmodeling/ch10.py`

<a id="cookmod-10-8-plastic-beam-plane-stress"></a>

### Sez. 10.8 Applications, 'Plastic Action in a Beam', Figg. 10.8-3, 10.8-4 - Plane-stress beam with bilinear plasticity: loading, unloading and residual stresses

🟡 · PDF page 307 · plasticity, plane_stress

Double-overhang beam supported at point A on the symmetry plane, loaded by q on the top face. The flexure formula gives sigma_x = sigma_Y at q = 5.83 N/mm. The book reports at full load at B sigma_x = 71, sigma_y = -8, sigma_e = 75 MPa, the residual stresses after unloading (-10 MPa at B, alternating along AB) and the load-displacement curves.

**Model**: 12 x 6 mesh of 2 x 2 mm Q4 on the symmetric half (24 x 12 mm, thickness 1), u = 0 along AB, A fixed; bilinear von Mises plasticity with kinematic hardening (feagent.inelastic.J2Plasticity.bilinear) with solve_inelastic: q from 0 to 8 N/mm in 16 steps and unloading in 8. Stresses at B extrapolated from the Gauss points.

![cookmod-10.8-plastic-beam-plane-stress](images/books/cookmod-10.8-plastic-beam-plane-stress.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| first-yield q (flexure formula) [N/mm] | book | 5.83 | 5.8333 | 0.06 % | ✅ |
| sigma_x at B, q = 8 N/mm [MPa] | book | 71 | 75.421 | 6.23 % | ✅ |
| sigma_x at B versus elastic-plastic beam theory [MPa] | exact | 76.621 | 75.421 | 1.57 % | ✅ |
| residual sigma_x at B after unloading [MPa] | book | -10 | -10.313 | 3.13 % | ✅ |
| residuals along AB from the top: compression, tension, tension, compression (1 = true) | book | 1 | 1 | 0.00 % | ✅ |
| v_C at full load (book plot not comparable) [mm] | book | - | -0.097195 | - | ✅ |

**Notes**: The book does not state the hardening rule (kinematic is used, which it recommends for metals) nor how it computes nodal stresses. feagent gives sigma_x at B 75.4 MPa at full load, close to elastic-plastic beam theory (76.6 MPa, section AB with M = 288 q); the book's 71 is 6 % lower (7 % tolerance). sigma_y at B is not compared: with Q4 the Gauss-point extrapolation does not rebuild the -8 of the face pressure. The residual at B (-10.3 against -10) and the alternating sign along AB in the upper part match. The displacements of the book's plot (v_C about 1.2 mm) are about ten times feagent's (0.097 mm), which agree with beam theory with E = 50 GPa: not compared (probably a different scale or modulus in the plot).

*Code*: `validation/books/cookmodeling/ch10.py`

<a id="cookmod-10-8-beam-gap-contact"></a>

### Sez. 10.8 Applications, 'Beam With Contact Nonlinearity', Fig. 10.8-5 - Beam with an initial gap at an intermediate support and lift-off at the end

✅ · PDF page 308 · contact, beam_2d

Beam pinned at A, loaded at B (100 mm), with a support at C (200 mm) with a 0.5 mm gap and a support at D (300 mm) initially in contact, both reacting only upwards. At P = 20 N: v_B = -1.03 mm, v_C = -0.502 mm, D lifts by 0.42 mm; support sequence A-D, A-C-D, A-C.

**Model**: 15 beams 20 mm long (4 x 4 mm section, E = 200 GPa); supports C and D as 5000 N/mm vertical springs to fixed nodes, with feagent.inelastic.Gap (0.5 mm gap at C, zero at D, compression only); P at B from 0 to 20 N in 40 steps with solve_inelastic.

![cookmod-10.8-beam-gap-contact](images/books/cookmod-10.8-beam-gap-contact.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| v_B at P = 20 N [mm] | book | -1.03 | -1.0323 | 0.22 % | ✅ |
| v_C at P = 20 N [mm] | book | -0.502 | -0.502 | 0.00 % | ✅ |
| lift of D at P = 20 N [mm] | book | 0.42 | 0.41888 | 0.27 % | ✅ |
| theory: P L^3 / (48 E I) + 0.25 [mm] | book | 1.03 | 1.0312 | 0.12 % | ✅ |
| gap at C closes between the steps bracketing 5.49 N (1 = true) | book | 1 | 1 | 0.00 % | ✅ |

**Notes**: Contact at C closes between 5.0 and 5.5 N (beam supported at A-D: 0.5 / 0.0911 = 5.49 N) and D lifts off between 12.5 and 13 N.

*Code*: `validation/books/cookmodeling/ch10.py`
