---
layout: default
title: "64 - Examples from Seshu"
parent: English
nav_order: 64
---

# 64 - Examples from Seshu

This page collects **all the worked examples** of the book *Textbook of Finite Element Analysis* (P. Seshu, PHI Learning, 2003) redone with feagent. For each example: reference in the book, feagent model, numerical comparison between the book value and feagent, notes (missing data, book misprints corrected, modelling differences). Texts are summarised in our own words; numerical data are given to redo the computation. The code of every example is in `validation/books/seshu/`; to run them again:

```bash
python -m validation.books.run_books seshu
python scripts/_gen_book_docs.py
```

**Outcome**: ✅ reproduced and compared; 🟡 partly reproduced (book data or results incomplete); 📐 derivation of formulas checked numerically; ⛔ not reproducible (reason in the notes). The *error* column is the largest relative error of the comparisons; *ref.* tells what the comparison is made against: book, exact solution or corrected value of a misprint.

**44 examples**: 21 reproduced, 2 partly, 19 derivations checked, 2 not reproducible; 422 numerical checks, 422 within tolerance.

## Summary

| Example | Title | Outcome | Checks | Max error |
|---|---|---|---|---|
| [Example 2.1](#seshu-ex-2-1) | Bar under uniform axial load: one-parameter weighted residuals | 📐 | 3/3 | 0.00 % |
| [Example 2.2](#seshu-ex-2-2) | Laminar flow on an inclined plane (weighted residuals) | 📐 | 3/3 | 0.00 % |
| [Example 2.3](#seshu-ex-2-3) | Cantilever under uniform load: weighted residuals | 📐 | 3/3 | 0.00 % |
| [Example 2.4](#seshu-ex-2-4) | Simply supported beam under uniform load: collocation and Galerkin with sines | 📐 | 6/6 | 1.50 % |
| [Example 2.5](#seshu-ex-2-5) | Long cylinder with uniform heat source (Galerkin) | 📐 | 3/3 | 0.00 % |
| [Example 2.6](#seshu-ex-2-6) | Bar with linearly varying load q = ax (cubic Galerkin) | 📐 | 4/4 | 0.00 % |
| [Example 2.7](#seshu-ex-2-7) | Simply supported rectangular plate under uniform load (one-term Galerkin) | 📐 | 4/4 | 0.86 % |
| [Example 2.8 + Tabella 2.1](#seshu-ex-2-8) | Aluminium pin fin: quadratic, cubic and quartic Galerkin | 📐 | 13/13 | 0.25 % |
| [Example 2.9](#seshu-ex-2-9) | Weak form for the bar with load q = ax, quadratic trial | 📐 | 7/7 | 0.00 % |
| [Example 2.10](#seshu-ex-2-10) | Simply supported beam in weak form with quadratic trial | 📐 | 4/4 | 0.00 % |
| [Example 2.11](#seshu-ex-2-11) | Assembly of two bar elements | 📐 | 4/4 | 0.00 % |
| [Example 2.12](#seshu-ex-2-12) | Tip-loaded steel rod (2 elements) | ✅ | 3/3 | 0.08 % |
| [Example 2.13](#seshu-ex-2-13) | Rod under distributed and concentrated forces (4 elements) | ✅ | 5/5 | 0.16 % |
| [Example 2.14](#seshu-ex-2-14) | Plane wall with uniform heat source (4 elements) | ✅ | 4/4 | 0.00 % |
| [Example 3.1](#seshu-ex-3-1) | Bar under uniform load by the Rayleigh-Ritz method | 📐 | 3/3 | 0.00 % |
| [Example 3.2](#seshu-ex-3-2) | Simply supported beam under uniform load by the Rayleigh-Ritz method | 📐 | 3/3 | 0.01 % |
| [Example 3.3 + Tabella 3.1](#seshu-ex-3-3) | Pin fin: quadratic Rayleigh-Ritz | 📐 | 7/7 | 0.09 % |
| [Example 4.10](#seshu-ex-4-10) | Pin fin with linear conduction-convection elements | ✅ | 9/9 | 0.00 % |
| [Example 4.1 + Tabelle 4.1-4.2](#seshu-ex-4-1) | Hanging rod under self-weight with 1, 2, 4, 8 and 16 linear elements | ✅ | 20/20 | 0.28 % |
| [Example 4.2](#seshu-ex-4-2) | Hanging rod with one quadratic bar element | ✅ | 6/6 | 0.00 % |
| [Example 4.3](#seshu-ex-4-3) | Effect of node numbering on the assembled stiffness matrix | 📐 | 4/4 | 0.00 % |
| [Example 4.4](#seshu-ex-4-4) | Thermal stresses in a bar (free and clamped) | ✅ | 4/4 | 0.00 % |
| [Example 4.5](#seshu-ex-4-5) | Statically indeterminate three-bar truss | ✅ | 2/2 | 0.00 % |
| [Example 4.6](#seshu-ex-4-6) | Cantilever with a single beam element (P, M, q0) | ✅ | 10/10 | 0.00 % |
| [Example 4.7](#seshu-ex-4-7) | Continuous beam on four supports (5 elements) | ✅ | 12/12 | 0.02 % |
| [Example 4.8](#seshu-ex-4-8) | Frame: steel cantilever supported by a pinned tie rod | ✅ | 5/5 | 0.37 % |
| [Example 4.9](#seshu-ex-4-9) | Simply supported beam with axial and transverse loads (2 frame elements) | ✅ | 4/4 | 0.03 % |
| [Example 5.1](#seshu-ex-5-1) | Thin plate in tension with two CST triangles | ✅ | 13/13 | 0.33 % |
| [Example 5.2](#seshu-ex-5-2) | Gauss integration of a polynomial | 📐 | 3/3 | 0.00 % |
| [Example 5.3](#seshu-ex-5-3) | Gauss integration of cos(pi x/2) | 📐 | 5/5 | 0.00 % |
| [Example 5.4](#seshu-ex-5-4) | The plate of Ex. 5.1 with one bilinear rectangular element | ✅ | 13/13 | 0.13 % |
| [Example 5.5](#seshu-ex-5-5) | Jacobian of a regular and a distorted quadrilateral | 📐 | 9/9 | 0.01 % |
| [Example 5.6 + Tabelle 5.3-5.4](#seshu-ex-5-6) | Cantilever modelled with Q4 and Q8 plane elements | ✅ | 17/17 | 1.11 % |
| [Example 5.7 (Fig. 5.42)](#seshu-ex-5-7) | Stress concentration around a circular hole | 🟡 | 7/7 | 1.48 % |
| [Sezione 5.8 (Fig. 5.43-5.46)](#seshu-sec-5-8-flow) | Potential flow around a cylinder between walls (stream function) | 🟡 | 4/4 | 0.62 % |
| [Example 6.1](#seshu-ex-6-1) | Lagrange's equations for a two-mass, three-spring system | 📐 | 5/5 | 0.00 % |
| [Example 6.2 + Tabella 6.1](#seshu-ex-6-2) | Axial frequencies of a fixed-free bar: lumped and consistent mass | ✅ | 51/51 | 0.32 % |
| [Example 6.3 + Tabella 6.2](#seshu-ex-6-3) | Frequencies of a simply supported beam: simple lumped, HRZ and consistent mass | ✅ | 58/58 | 0.33 % |
| [Example 6.4](#seshu-ex-6-4) | Eigenvalues of the 3-element bar by the generalised Jacobi method | ✅ | 15/15 | 100.00 % |
| [Example 6.5](#seshu-ex-6-5) | Same problem by simultaneous vector iteration | ✅ | 15/15 | 234.51 % |
| [Example 6.6](#seshu-ex-6-6) | Free response of a 2-DOF system by mode superposition | ✅ | 11/11 | 0.00 % |
| [Example 6.7 + Tabella 6.3](#seshu-ex-6-7) | Transient temperature in the pin fin (4 elements, trapezoidal rule) | ✅ | 41/41 | 0.01 % |
| [Sezione 7.1 (Tabella 7.1)](#seshu-sec-7-1-crankshaft) | Torsional vibrations of a crankshaft: beams versus 3D model | ⛔ | 0/0 | - |
| [Sezione 7.2 (Fig. 7.13-7.16)](#seshu-sec-7-2-vessel) | Pressure vessel with threaded closures (axisymmetric) | ⛔ | 0/0 | - |

## Chapter 2

<a id="seshu-ex-2-1"></a>

### Example 2.1 - Bar under uniform axial load: one-parameter weighted residuals

📐 · PDF page 27 · bar

Equation AE u'' + q0 = 0 with u(0) = 0 and zero force at the tip. The quadratic trial satisfying both conditions zeroes the residual with c2 = -q0/(2AE) and equals the exact solution q0 (2Lx - x^2)/(2AE). Test values: L = 1 m, A = 30 mm², E = 200 GPa, q0 = 100 N/m.

**Model**: Method: quadratic trial c2 (x^2 - 2Lx) with the residual set to zero (numpy). feagent model: rod of 4 L2 bars, fixed end, uniform axial load with consistent nodal forces.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| c2 from zero residual [1/m] | book | -8.3333e-06 | -8.3333e-06 | 0.00 % | ✅ |
| u(L), 4 feagent bars [m] | book | 8.3333e-06 | 8.3333e-06 | 0.00 % | ✅ |
| u(L/2), 4 feagent bars [m] | exact | 6.2500e-06 | 6.2500e-06 | 0.00 % | ✅ |

**Notes**: Symbolic example: numerical values chosen for the check.

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-2"></a>

### Example 2.2 - Laminar flow on an inclined plane (weighted residuals)

📐 · PDF page 28 · other

Newtonian film flowing down an inclined plane: mu v'' + rho g cos(theta) = 0, zero shear at the free surface and no slip at the wall. The exact solution is the parabola rho g cos(theta) (L^2 - x^2)/(2 mu). Test values: water (mu = 1e-3 Pa s, rho = 1000 kg/m³), theta = 60°, L = 2 mm.

**Model**: Method: quadratic trial c2 (x^2 - L^2) with zero residual. feagent model: 1D FieldModel('generic') with 8 L2 elements, k = mu, source rho g cos(theta), zero velocity at the wall (x = L), zero flux at the free surface (x = 0).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| v(0) from quadratic trial [m/s] | exact | 9.81 | 9.81 | 0.00 % | ✅ |
| v(0), 8 feagent elements [m/s] | exact | 9.81 | 9.81 | 0.00 % | ✅ |
| v(L/2), 8 feagent elements [m/s] | exact | 7.3575 | 7.3575 | 0.00 % | ✅ |

**Notes**: 1D Poisson equation: with feagent's generic field problem the analogy is direct (k = mu, Q = rho g cos theta). Numerical values chosen for the check.

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-3"></a>

### Example 2.3 - Cantilever under uniform load: weighted residuals

📐 · PDF page 29 · beam

Cantilever EI v'''' = q0, clamped at x = 0 with zero moment and shear at the tip. The quartic satisfying the four conditions zeroes the residual with c4 = q0/(24EI): it is the exact solution, tip deflection q0L^4/(8EI). Test values: L = 2, EI = 1000, q0 = 10.

**Model**: Method: quartic polynomial c4 (x^4 - 4Lx^3 + 6L^2x^2) with zero residual. feagent model: cantilever of 4 Euler-Bernoulli beams with uniform load.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| v(L) from polynomial trial | book | 0.02 | 0.02 | 0.00 % | ✅ |
| v(L), 4 feagent beams | exact | 0.02 | 0.02 | 0.00 % | ✅ |
| v(L/2), 4 feagent beams | exact | 0.0070833 | 0.0070833 | 0.00 % | ✅ |

**Notes**: Symbolic example: numerical values chosen for the check.

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-4"></a>

### Example 2.4 - Simply supported beam under uniform load: collocation and Galerkin with sines

📐 · PDF page 30 · beam

Simply supported beam EI v'''' = q0 approximated by sine series. Collocation at L/4: c1 = sqrt(2) q0 L^4/(pi^4 EI); two-term collocation at L/4 and L/3: c3 = 0.00003289 q0L^4/EI; one-term Galerkin: c1 = 0.013071 q0L^4/EI, 0.38 % error on the midspan deflection with respect to 5 q0L^4/(384 EI).

**Model**: Method (numpy): one- and two-term collocation with sin(pi x/L), sin(3 pi x/L) and one-term Galerkin (integrals by Gauss quadrature). feagent model: simply supported beam of 8 Euler-Bernoulli beams.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| c1, collocation at L/4 [q0L^4/EI] | book | 0.014518 | 0.014518 | 0.00 % | ✅ |
| c1, collocation at L/4 and L/3 [q0L^4/EI] | book | 0.011854 | 0.011854 | 0.00 % | ✅ |
| c3, collocation at L/4 and L/3 [q0L^4/EI] | book | 3.2890e-05 | 3.2891e-05 | 0.00 % | ✅ |
| c1, one-term Galerkin [q0L^4/EI] | book | 0.013071 | 0.013071 | 0.00 % | ✅ |
| midspan deflection error [%] | book | 0.38 | 0.3857 | 1.50 % | ✅ |
| v(L/2), 8 feagent beams [q0L^4/EI] | exact | 0.013021 | 0.013021 | 0.00 % | ✅ |

**Notes**: The text of Eq. 2.30 names collocation points L/4 and L/2, but the printed coefficients (c1 = 2 q0L^4/(sqrt(3) pi^4 EI), c3 = 0.00003289 q0L^4/EI) and the caption of Fig. 2.8 correspond to L/4 and L/3: L/2 would give c3 = 0.0000262. L/4 and L/3 are used. Dimensionless coefficients (q0 = L = EI = 1).

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-5"></a>

### Example 2.5 - Long cylinder with uniform heat source (Galerkin)

📐 · PDF page 36 · heat_steady

Long cylinder of radius R with uniform generation q0 and wall at Tw. One-parameter Galerkin gives c2 = -q0/(4k), i.e. the exact solution T - Tw = q0 (R^2 - r^2)/(4k). Test values: R = 0.05 m, k = 15 W/m°C, q0 = 1e6 W/m³, Tw = 80 °C.

**Model**: Method (numpy): trial T = Tw - (q0R/2k)(r-R) + c2 (r-R)^2, Galerkin weighted by r dr. feagent model: axisymmetric FieldModel('heat'), radial strip of 20 Q8 elements with uniform source and prescribed temperature at r = R.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Galerkin c2 [°C/m²] | exact | -16667 | -16667 | 0.00 % | ✅ |
| Axis T, 20 axisymmetric Q8 [°C] | exact | 121.67 | 121.67 | 0.00 % | ✅ |
| T at r = R/2 [°C] | exact | 111.25 | 111.25 | 0.00 % | ✅ |

**Notes**: Symbolic example, numerical values chosen. The printed result is barely legible in the scan; c2 is obtained from the calculation.

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-6"></a>

### Example 2.6 - Bar with linearly varying load q = ax (cubic Galerkin)

📐 · PDF page 37 · bar

AE u'' + ax = 0, u(0) = 0, zero force at x = L. Two-term Galerkin gives c2 = 0 and c3 = -a/(6AE): the exact solution a (3L^2x - x^3)/(6AE), with u(L) = aL^3/(3AE). Test values: L = 1 m, AE = 6e6 N, a = 1000 N/m².

**Model**: Method (numpy): Galerkin with the functions (x^2 - 2Lx) and (x^3 - 3L^2x). feagent model: 4 L2 bars with linear load given by nodal values (consistent forces).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Galerkin c2 [1/m] | book | 0 | 3.0316e-19 | 0.00 % | ✅ |
| Galerkin c3 [1/m²] | book | -2.7778e-05 | -2.7778e-05 | 0.00 % | ✅ |
| u(L), 4 feagent bars [m] | book | 5.5556e-05 | 5.5556e-05 | 0.00 % | ✅ |
| u(L/4), 4 feagent bars [m] | exact | 2.0399e-05 | 2.0399e-05 | 0.00 % | ✅ |

**Notes**: Symbolic example, numerical values chosen.

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-7"></a>

### Example 2.7 - Simply supported rectangular plate under uniform load (one-term Galerkin)

📐 · PDF page 39 · plate

Plate simply supported on four edges under q0: the Galerkin term c11 = 16 q0 / (pi^6 D [(1/a)^2 + (1/b)^2]^2). For the square plate the centre deflection is 0.00416 q0a^4/D against Navier's 0.00406 q0a^4/D.

**Model**: Method (numpy): Galerkin with sin(pi x/a) sin(pi y/b), integrals by Gauss quadrature. feagent model: square plate, 16x16 thin Q4 shells (a/t = 100), simple support (uz = 0) on the boundary.

![seshu-ex-2.7](images/books/seshu-ex-2.7.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Galerkin c11, a x b = 1 x 1.5 m [m] | book | 4.3552e-04 | 4.3552e-04 | 0.00 % | ✅ |
| Centre deflection, one-term Galerkin [q0a^4/D] | book | 0.00416 | 0.0041606 | 0.02 % | ✅ |
| Centre deflection, Navier series [q0a^4/D] | exact | 0.00406 | 0.0040624 | 0.06 % | ✅ |
| Centre deflection, 16x16 feagent shells [m] | exact | 2.2180e-04 | 2.2371e-04 | 0.86 % | ✅ |

**Notes**: Test values: a = b = 1 m (and b = 1.5 m for the rectangular case), t = 10 mm, E = 200 GPa, nu = 0.3, q0 = 1 kPa. The exact reference is Navier's series.

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-8"></a>

### Example 2.8 + Tabella 2.1 - Aluminium pin fin: quadratic, cubic and quartic Galerkin

📐 · PDF page 40 · heat_steady

Pin fin d = 1 mm, L = 50 mm, k = 200 W/m°C, h = 20 W/m²°C, wall at 300 °C, air at 30 °C: T'' = 400 (T - 30). The book derives the Galerkin coefficients for quadratic, cubic and quartic trials and compares them with the exact cosh solution (Table 2.1).

**Model**: Method (numpy): Galerkin with global polynomials satisfying T(0) = 300 °C and insulated tip. feagent model: the fin of Ex. 4.10 (FieldModel, L2 elements with lateral convection) with 32 elements.

![seshu-ex-2.8](images/books/seshu-ex-2.8.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| c2, quadratic trial (printed 38751.43) [°C/m²] | corrected | 38571 | 38571 | 0.00 % | ✅ |
| Table 2.1 T(L) recomputed with the printed c2 [°C] | book | 203.12 | 203.12 | 0.00 % | ✅ |
| c2, cubic trial [°C/m²] | book | 48860 | 48860 | 0.00 % | ✅ |
| c3, cubic trial [°C/m³] | book | -1.0923e+05 | -1.0923e+05 | 0.00 % | ✅ |
| c2, quartic trial [°C/m²] | book | 53703 | 53703 | 0.00 % | ✅ |
| c3, quartic trial [°C/m³] | book | -2.5567e+05 | -2.5567e+05 | 0.00 % | ✅ |
| c4, quartic trial [°C/m⁴] | book | 1.3200e+06 | 1.3167e+06 | 0.25 % | ✅ |
| T at x = 0.025 m, cubica trial [°C] | book | 227.16 | 227.16 | 0.00 % | ✅ |
| T at x = 0.05 m, cubica trial [°C] | book | 205.16 | 205.16 | 0.00 % | ✅ |
| T at x = 0.025 m, quartica stampata trial [°C] | book | 227.27 | 227.27 | 0.00 % | ✅ |
| T at x = 0.05 m, quartica stampata trial [°C] | book | 204.91 | 204.91 | 0.00 % | ✅ |
| T at x = 0.025 m, exact [°C] | book | 227.31 | 227.31 | 0.00 % | ✅ |
| Tip T, 32 feagent elements [°C] | exact | 204.97 | 204.97 | 0.00 % | ✅ |

**Notes**: Book typo: the quadratic-trial c2 is 38571.43 (closed form c2 = (2/3) 270 m^2 / (4/3 + 8 m^2 L^2/15)), not 38751.43 (swapped digits); the quadratic column of Table 2.1 is computed with the wrong value (tip 203.12 instead of 203.57 °C). Cubic and quartic coefficients agree; the quartic column uses c4 rounded to 1.32e6, with the full c4 the tip is at 204.97 °C instead of 204.91. Temperatures compared at x = 0.025 and 0.05 m.

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-9"></a>

### Example 2.9 - Weak form for the bar with load q = ax, quadratic trial

📐 · PDF page 43 · bar

Same problem as Ex. 2.6 solved with the weak form and a quadratic trial, which need satisfy only u(0) = 0: u = aL (7Lx - 3x^2)/(12AE). With the original weighted residuals the same trial gives 5aL (2Lx - x^2)/(16AE). Linear elements converge to the exact solution.

**Model**: Method (numpy): weak form with u = c1 x + c2 x^2 and weights x, x^2; original weighted residuals with c (x^2 - 2Lx). feagent model: 1, 2, 4 and 8 L2 bars.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| c1, weak form [aL^2/AE] | book | 0.58333 | 0.58333 | 0.00 % | ✅ |
| c2, weak form [aL/AE] | book | -0.25 | -0.25 | 0.00 % | ✅ |
| c, original weighted residuals [aL/AE] | book | -0.3125 | -0.3125 | 0.00 % | ✅ |
| u(L), 1 feagent bars [aL^3/AE] | exact | 0.33333 | 0.33333 | 0.00 % | ✅ |
| u(L), 2 feagent bars [aL^3/AE] | exact | 0.33333 | 0.33333 | 0.00 % | ✅ |
| u(L), 4 feagent bars [aL^3/AE] | exact | 0.33333 | 0.33333 | 0.00 % | ✅ |
| u(L), 8 feagent bars [aL^3/AE] | exact | 0.33333 | 0.33333 | 0.00 % | ✅ |

**Notes**: Dimensionless coefficients (a = L = AE = 1). feagent's L2 bars with consistent load are exact at the nodes.

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-10"></a>

### Example 2.10 - Simply supported beam in weak form with quadratic trial

📐 · PDF page 48 · beam

Simply supported beam under uniform load solved in weak form with the parabola x(L - x) alone: c = q0L^2/(24EI). The midspan deflection is 20 % low, but the support rotations equal the exact q0L^3/(24EI).

**Model**: Method (numpy): weak form int EI v'' W'' = int W q with v = W = x(L - x). feagent model: 4 simply supported Euler-Bernoulli beams.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| coefficient c [q0L^2/EI] | book | 0.041667 | 0.041667 | 0.00 % | ✅ |
| midspan deflection error [%] | book | 20 | 20 | 0.00 % | ✅ |
| Support rotation, quadratic trial [q0L^3/EI] | book | 0.041667 | 0.041667 | 0.00 % | ✅ |
| Support rotation, 4 feagent beams [q0L^3/EI] | exact | 0.041667 | 0.041667 | 0.00 % | ✅ |

**Notes**: Dimensionless coefficients (q0 = L = EI = 1).

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-11"></a>

### Example 2.11 - Assembly of two bar elements

📐 · PDF page 61 · bar

Rod of length 2l split into two linear elements with uniform load q0: the assembled matrix is AE/l [[1,-1,0],[-1,2,-1],[0,-1,1]] and the consistent load vector q0 l [1/2, 1, 1/2].

**Model**: feagent model: 2 L2 bars of length l, assembled stiffness matrix (Model.assemble_stiffness) and load vector (Model.assemble_loads) on the ux DOFs.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Max deviation of assembled matrix [AE/l] | book | 0 | 0 | 0.00 % | ✅ |
| Assembled K22 [AE/l] | book | 2 | 2 | 0.00 % | ✅ |
| Consistent force, node 1 [q0 l] | book | 0.5 | 0.5 | 0.00 % | ✅ |
| Consistent force, node 2 [q0 l] | book | 1 | 1 | 0.00 % | ✅ |

**Notes**: Test values: l = 0.5 m, AE = 6e6 N, q0 = 100 N/m.

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-12"></a>

### Example 2.12 - Tip-loaded steel rod (2 elements)

✅ · PDF page 65 · bar

Steel rod 1 m long, 3 x 10 mm section, E = 200 GPa, with 100 N at the tip: nodal displacements and support reaction.

**Model**: feagent model: 2 L2 bars of 0.5 m, fixed at x = 0, axial tip force.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u2 [mm] | book | 0.00834 | 0.0083333 | 0.08 % | ✅ |
| u3 [mm] | book | 0.01667 | 0.016667 | 0.02 % | ✅ |
| Reaction R1 [N] | book | -100 | -100 | 0.00 % | ✅ |

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-13"></a>

### Example 2.13 - Rod under distributed and concentrated forces (4 elements)

✅ · PDF page 66 · bar

The rod of Ex. 2.12 with 100 N/m on the first half and 100 N at x = 0.75 m: nodal displacements (nodes 4 and 5 move together) and a 150 N reaction.

**Model**: feagent model: 4 L2 bars of 0.25 m, uniform axial load on the first two, nodal force at node 4.

![seshu-ex-2.13](images/books/seshu-ex-2.13.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u2 [mm] | book | 0.00573 | 0.0057292 | 0.01 % | ✅ |
| u3 [mm] | book | 0.0104 | 0.010417 | 0.16 % | ✅ |
| u4 [mm] | book | 0.0146 | 0.014583 | 0.11 % | ✅ |
| u5 [mm] | book | 0.0146 | 0.014583 | 0.11 % | ✅ |
| Reaction R1 [N] | book | -150 | -150 | 0.00 % | ✅ |

*Code*: `validation/books/seshu/ch02.py`

<a id="seshu-ex-2-14"></a>

### Example 2.14 - Plane wall with uniform heat source (4 elements)

✅ · PDF page 69 · heat_steady

Wall of thickness 2L with uniform generation q0 and faces at Tw: interior temperatures T2 = T4 = 3q0L^2/(8k) + Tw and T3 = q0L^2/(2k) + Tw, equal to the exact solution.

**Model**: feagent model: 1D FieldModel('heat') with 4 L2 elements of length L/2, uniform source, temperature prescribed on both faces.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| T2 [°C] | book | 96.875 | 96.875 | 0.00 % | ✅ |
| T3 [°C] | book | 112.5 | 112.5 | 0.00 % | ✅ |
| T4 [°C] | book | 96.875 | 96.875 | 0.00 % | ✅ |
| Heat flow out of one face (= q0 L) [W/m²] | book | 50000 | 50000 | 0.00 % | ✅ |

**Notes**: Symbolic example: L = 0.05 m, k = 20 W/m°C, q0 = 1e6 W/m³, Tw = 50 °C.

*Code*: `validation/books/seshu/ch02.py`

## Chapter 3

<a id="seshu-ex-3-1"></a>

### Example 3.1 - Bar under uniform load by the Rayleigh-Ritz method

📐 · PDF page 85 · bar

Fixed-free bar under uniform axial load q0. The quadratic trial, satisfying only the essential condition u(0) = 0, gives c1 = q0L/AE and c2 = -q0/(2AE), i.e. the exact solution of Ex. 2.1.

**Model**: Method (numpy): total potential energy with u = c1 x + c2 x^2, stationarity with respect to c1 and c2. feagent model: 4 fixed L2 bars with uniform load.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| c1 [q0L/AE] | book | 1 | 1 | 0.00 % | ✅ |
| c2 [q0/AE] | book | -0.5 | -0.5 | 0.00 % | ✅ |
| u(L), 4 feagent bars [q0L^2/AE] | exact | 0.5 | 0.5 | 0.00 % | ✅ |

**Notes**: Dimensionless coefficients (q0 = L = AE = 1).

*Code*: `validation/books/seshu/ch03.py`

<a id="seshu-ex-3-2"></a>

### Example 3.2 - Simply supported beam under uniform load by the Rayleigh-Ritz method

📐 · PDF page 87 · beam

Simply supported beam under uniform load: one-term sine Rayleigh-Ritz gives c1 = 4 q0L^4/(pi^5 EI) = 0.01307 q0L^4/EI, the same as the Galerkin result of Ex. 2.4, against the exact deflection 5/384 = 0.01302.

**Model**: Method (numpy): potential energy with v = c1 sin(pi x/L), integrals by Gauss quadrature. feagent model: 8 simply supported Euler-Bernoulli beams.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Rayleigh-Ritz c1 [q0L^4/EI] | book | 0.01307 | 0.013071 | 0.01 % | ✅ |
| c1 = 4/pi^5 [q0L^4/EI] | exact | 0.013071 | 0.013071 | 0.00 % | ✅ |
| v(L/2), 8 feagent beams [q0L^4/EI] | exact | 0.013021 | 0.013021 | 0.00 % | ✅ |

**Notes**: Dimensionless coefficients (q0 = L = EI = 1).

*Code*: `validation/books/seshu/ch03.py`

<a id="seshu-ex-3-3"></a>

### Example 3.3 + Tabella 3.1 - Pin fin: quadratic Rayleigh-Ritz

📐 · PDF page 89 · heat_steady

The fin of Ex. 2.8 solved by minimising the functional: the trial need satisfy only T(0) = 300 °C (the insulated tip is a natural condition). The book finds c1 = -3923.36 and c2 = 40498.44 and compares with Galerkin and the exact solution (Table 3.1).

**Model**: Method (numpy): conduction functional with lateral convection, trial T = 300 + c1 x + c2 x^2. feagent model: fin of Ex. 4.10 with 32 L2 elements.

![seshu-ex-3.3](images/books/seshu-ex-3.3.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| c1 [°C/m] | book | -3923.4 | -3921.6 | 0.04 % | ✅ |
| c2 [°C/m²] | book | 40498 | 40461 | 0.09 % | ✅ |
| T at x = 0.01 m, Rayleigh-Ritz [°C] | book | 264.82 | 264.83 | 0.00 % | ✅ |
| T at x = 0.025 m, Rayleigh-Ritz [°C] | book | 227.23 | 227.25 | 0.01 % | ✅ |
| T at x = 0.04 m, Rayleigh-Ritz [°C] | book | 207.86 | 207.87 | 0.01 % | ✅ |
| T at x = 0.05 m, Rayleigh-Ritz [°C] | book | 205.08 | 205.07 | 0.00 % | ✅ |
| T at x = 0.025 m, 32 feagent elements [°C] | exact | 227.31 | 227.3 | 0.00 % | ✅ |

**Notes**: The computed coefficients (c1 = -3921.61, c2 = 40461.1) differ from the printed ones by 0.04 % and 0.09 %: the book values satisfy the first stationarity equation with m^2 = 400.0 but the second with m^2 = 399.78, a sign of rounding in the hand calculation; the table temperatures agree within 0.01 °C. The Galerkin column of Table 3.1 repeats the wrong quadratic c2 of Ex. 2.8 (see that case): only the Rayleigh-Ritz column is compared here. At the tip the book prints 205.08 °C (cut in the scan), compared to 0.02 °C.

*Code*: `validation/books/seshu/ch03.py`

## Chapter 4

<a id="seshu-ex-4-10"></a>

### Example 4.10 - Pin fin with linear conduction-convection elements

✅ · PDF page 144 · heat_steady

Aluminium pin fin (d = 1 mm, L = 50 mm, k = 200 W/m°C) protruding from a wall at 300 °C into air at 30 °C (h = 20 W/m²°C), insulated tip. The book solves it with 1, 2 and 4 linear elements and compares with the exact solution.

**Model**: FieldModel('heat') with 1, 2 and 4 L2 elements (area and perimeter of the round section), lateral convection on every element, prescribed temperature at the base.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| T2, 1 elements [°C] | book | 198.75 | 198.75 | 0.00 % | ✅ |
| T2, 2 elements [°C] | book | 226.19 | 226.18 | 0.00 % | ✅ |
| T3, 2 elements [°C] | book | 203.55 | 203.55 | 0.00 % | ✅ |
| T2, 4 elements [°C] | book | 256.37 | 256.37 | 0.00 % | ✅ |
| T3, 4 elements [°C] | book | 227.03 | 227.03 | 0.00 % | ✅ |
| T4, 4 elements [°C] | book | 210.14 | 210.14 | 0.00 % | ✅ |
| T5, 4 elements [°C] | book | 204.63 | 204.63 | 0.00 % | ✅ |
| Tip T, exact solution [°C] | book | 204.97 | 204.97 | 0.00 % | ✅ |
| Tip T, 64 elements [°C] | exact | 204.97 | 204.97 | 0.00 % | ✅ |

*Code*: `validation/books/seshu/ch04.py`

<a id="seshu-ex-4-1"></a>

### Example 4.1 + Tabelle 4.1-4.2 - Hanging rod under self-weight with 1, 2, 4, 8 and 16 linear elements

✅ · PDF page 104 · bar

Steel rod 1 m long, A = 5 cm², hanging from its upper end. Nodal displacements are exact for every mesh (tip 0.191 µm), stresses are piecewise constant (1/2; 3/4 and 1/4; 7/8 ... 1/8 of rho g L) and their nodal average equals the exact value; inside the elements the displacement converges (Table 4.2, x = 0.33 L).

**Model**: feagent model: 1, 2, 4, 8 and 16 L2 bars, self-weight as body force (add_solid_body_force, consistent nodal forces), upper end fixed.

![seshu-ex-4.1](images/books/seshu-ex-4.1.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Tip u, 1 elements [µm] | book | 0.191 | 0.19129 | 0.15 % | ✅ |
| Tip u, 2 elements [µm] | book | 0.191 | 0.19129 | 0.15 % | ✅ |
| Tip u, 4 elements [µm] | book | 0.191 | 0.19129 | 0.15 % | ✅ |
| Tip u, 8 elements [µm] | book | 0.191 | 0.1913 | 0.15 % | ✅ |
| Tip u, 16 elements [µm] | book | 0.191 | 0.1913 | 0.15 % | ✅ |
| u at x = 0.25 m, 4 elements [µm] | book | 0.0836 | 0.083692 | 0.11 % | ✅ |
| u at x = 0.0625 m, 16 elements [µm] | book | 0.0231 | 0.023165 | 0.28 % | ✅ |
| Stress element 1, 1 elements [Pa] | book | 38259 | 38259 | 0.00 % | ✅ |
| Stress element 1, 2 elements [Pa] | book | 57388 | 57388 | 0.00 % | ✅ |
| Stress element 2, 2 elements [Pa] | book | 19130 | 19129 | 0.00 % | ✅ |
| Stress element 1, 4 elements [Pa] | book | 66953 | 66953 | 0.00 % | ✅ |
| Stress element 2, 4 elements [Pa] | book | 47824 | 47824 | 0.00 % | ✅ |
| Stress element 3, 4 elements [Pa] | book | 28694 | 28694 | 0.00 % | ✅ |
| Stress element 4, 4 elements [Pa] | book | 9564.8 | 9564.7 | 0.00 % | ✅ |
| Averaged stress at node x = L/2 (4 el.) [Pa] | exact | 38259 | 38259 | 0.00 % | ✅ |
| u(0.33 L)/exact, 1 elements | book | 0.598 | 0.5988 | 0.13 % | ✅ |
| u(0.33 L)/exact, 2 elements | book | 0.898 | 0.8982 | 0.02 % | ✅ |
| u(0.33 L)/exact, 4 elements | book | 0.975 | 0.97532 | 0.03 % | ✅ |
| u(0.33 L)/exact, 8 elements | book | 0.993 | 0.99347 | 0.05 % | ✅ |
| u(0.33 L)/exact, 16 elements | book | 0.998 | 0.99857 | 0.06 % | ✅ |

**Notes**: g = 9.81 m/s² (implicit in the book).

*Code*: `validation/books/seshu/ch04.py`

<a id="seshu-ex-4-2"></a>

### Example 4.2 - Hanging rod with one quadratic bar element

✅ · PDF page 113 · bar

The rod of Ex. 4.1 with a single quadratic element: consistent nodal forces rho A g l (1/6, 1/6, 4/6), exact displacements at the tip (rho g L^2/(2E)) and at midspan (3 rho g L^2/(8E)) and exact linear stress.

**Model**: feagent model: one L3 bar (nodes: ends, then midside node), self-weight as body force.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Tip u [m] | book | 1.9129e-07 | 1.9129e-07 | 0.00 % | ✅ |
| Midspan u [m] | book | 1.4347e-07 | 1.4347e-07 | 0.00 % | ✅ |
| Consistent force, midside node [rho A g l] | book | 0.66667 | 0.66667 | 0.00 % | ✅ |
| Consistent force, end node [rho A g l] | book | 0.16667 | 0.16667 | 0.00 % | ✅ |
| Stress at x = 0.25 L [Pa] | exact | 57388 | 57388 | 0.00 % | ✅ |
| Stress at x = 0.75 L [Pa] | exact | 19130 | 19129 | 0.00 % | ✅ |

*Code*: `validation/books/seshu/ch04.py`

<a id="seshu-ex-4-3"></a>

### Example 4.3 - Effect of node numbering on the assembled stiffness matrix

📐 · PDF page 115 · bar

The same six-element rod numbered in two ways: with sequential numbering the matrix is tridiagonal (half-bandwidth 1), with the alternating one the nonzero terms move away from the diagonal (half-bandwidth 6). Both matrices are compared with Figs. 4.9 and 4.10.

**Model**: feagent model: 6 L2 bars with AE/l = 1, nodes numbered in sequence (mesh 1) and in the order 1, 7, 2, 6, 3, 5, 4 (mesh 2); matrix from Model.assemble_stiffness on the ux DOFs.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Max deviation, mesh 1 | book | 0 | 0 | 0.00 % | ✅ |
| Max deviation, mesh 2 | book | 0 | 0 | 0.00 % | ✅ |
| Half-bandwidth, mesh 1 | book | 1 | 1 | 0.00 % | ✅ |
| Half-bandwidth, mesh 2 | book | 6 | 6 | 0.00 % | ✅ |

**Notes**: Conceptual example on banded storage; checked by comparing the matrices assembled by feagent with the printed ones.

*Code*: `validation/books/seshu/ch04.py`

<a id="seshu-ex-4-4"></a>

### Example 4.4 - Thermal stresses in a bar (free and clamped)

✅ · PDF page 118 · bar

Rod uniformly heated by T: with one free end it lengthens by L alpha T without stress; clamped at both ends the stress is -E alpha T.

**Model**: feagent model: 2 L2 bars with uniform temperature change (add_solid_thermal), fixed-free and fixed-fixed.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u2, fixed-free [m] | book | 3.0000e-04 | 3.0000e-04 | 0.00 % | ✅ |
| u3, fixed-free [m] | book | 6.0000e-04 | 6.0000e-04 | 0.00 % | ✅ |
| Stress, fixed-free [Pa] | book | 0 | -2.1684e-08 | 0.00 % | ✅ |
| Stress, fixed-fixed [Pa] | book | -1.2000e+08 | -1.2000e+08 | 0.00 % | ✅ |

**Notes**: Symbolic example: L = 1 m, A = 1 cm², E = 200 GPa, alpha = 1.2e-5 1/°C, T = 50 °C.

*Code*: `validation/books/seshu/ch04.py`

<a id="seshu-ex-4-5"></a>

### Example 4.5 - Statically indeterminate three-bar truss

✅ · PDF page 121 · truss

Vertical member of length L and two members inclined by alpha, hung from the same ceiling and joined at the bottom, vertical load P: vertical displacement P L / (AE (1 + 2 cos^3 alpha)), zero horizontal.

**Model**: feagent model: 3 truss members (add_truss) in the X-Y plane, support nodes fixed, free node with uz restrained.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Vertical displacement of node 3 [m] | book | 2.1748e-04 | 2.1748e-04 | 0.00 % | ✅ |
| Horizontal displacement of node 3 [m] | book | 0 | 0 | 0.00 % | ✅ |

**Notes**: Symbolic example: L = 1 m, alpha = 30°, A = 1 cm², E = 200 GPa, P = 10 kN. In the OCR the exponent reads cos^2: on the page it is cos^3, consistent with the assembled stiffness.

*Code*: `validation/books/seshu/ch04.py`

<a id="seshu-ex-4-6"></a>

### Example 4.6 - Cantilever with a single beam element (P, M, q0)

✅ · PDF page 132 · beam

Cantilever with tip force and couple and uniform load, solved with a single element: tip deflection and rotation are exact for all three loads; inside, the Hermite cubic is exact for P and M but not for q0 (at midspan q0L^4/(24EI) against 17 q0L^4/(384EI)).

**Model**: feagent model: one clamped Euler-Bernoulli beam element, three load cases (force P, couple M, uniform load q0 with consistent forces).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Tip deflection, load P | exact | 0.026667 | 0.026667 | 0.00 % | ✅ |
| Tip rotation, load P | exact | 0.02 | 0.02 | 0.00 % | ✅ |
| Interpolated deflection at L/2, load P | exact | 0.0083333 | 0.0083333 | 0.00 % | ✅ |
| Tip deflection, load M | exact | 0.01 | 0.01 | 0.00 % | ✅ |
| Tip rotation, load M | exact | 0.01 | 0.01 | 0.00 % | ✅ |
| Interpolated deflection at L/2, load M | exact | 0.0025 | 0.0025 | 0.00 % | ✅ |
| Tip deflection, load q | exact | 0.006 | 0.006 | 0.00 % | ✅ |
| Tip rotation, load q | exact | 0.004 | 0.004 | 0.00 % | ✅ |
| Interpolated deflection at L/2, load q | book | 0.002 | 0.002 | 0.00 % | ✅ |
| Exact deflection at L/2 under q0 (17/384) | exact | 0.002125 | 0.002125 | 0.00 % | ✅ |

**Notes**: Symbolic example: L = 2, EI = 1000, P = 10, M = 5, q0 = 3. The interior displacement is interpolated with the element's Hermite functions; for q0 the reference is the book's Eq. 4.132 (q0L^4/(24EI) at midspan).

*Code*: `validation/books/seshu/ch04.py`

<a id="seshu-ex-4-7"></a>

### Example 4.7 - Continuous beam on four supports (5 elements)

✅ · PDF page 133 · beam

Continuous beam A-B (2 m, clamped at A), B-C (1 m), C-D (2 m) with 1 kN at the middle of AB and 12 kN/m on CD. Nodal displacements and rotations with EI = 1 and reactions (Fig. 4.21).

**Model**: feagent model: 5 Euler-Bernoulli beams of 1 m (EI = 1), clamped at A, supports at B, C, D; 1 kN force and 12 kN/m uniform load (consistent forces).

![seshu-ex-4.7](images/books/seshu-ex-4.7.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| v2 [m (EI=1)] | book | -0.157 | -0.15697 | 0.02 % | ✅ |
| theta2 [rad (EI=1)] | book | -0.1153 | -0.1153 | 0.00 % | ✅ |
| theta3 [rad (EI=1)] | book | 0.4612 | 0.46121 | 0.00 % | ✅ |
| theta4 [rad (EI=1)] | book | -1.2586 | -1.2586 | 0.00 % | ✅ |
| v5 [m (EI=1)] | book | -1.472 | -1.472 | 0.00 % | ✅ |
| theta5 [rad (EI=1)] | book | -0.3427 | -0.34267 | 0.01 % | ✅ |
| theta6 [rad (EI=1)] | book | 2.6293 | 2.6293 | 0.00 % | ✅ |
| Reaction at A [kN] | book | 1.1918 | 1.1918 | 0.00 % | ✅ |
| Fixed-end moment at A [kNm] | book | 0.7112 | 0.71121 | 0.00 % | ✅ |
| Reaction at B (downwards) [kN] | book | 4.9763 | 4.9763 | 0.00 % | ✅ |
| Reaction at C net of the equivalent nodal force [kN] | book | 12.841 | 12.841 | 0.00 % | ✅ |
| Reaction at D net of the equivalent nodal force [kN] | book | 3.944 | 3.944 | 0.00 % | ✅ |

**Notes**: Eqs. 4.136 write the loads in N, but the printed displacements correspond to loads in kN with EI = 1. In Fig. 4.21 the reactions at C and D (12.8405 and 3.944) are those of the discrete system, net of the 6 kN equivalent nodal forces of the distributed load: the true reactions are 18.8405 and 9.944 kN. At B the reaction (4.9763 kN) points downwards.

*Code*: `validation/books/seshu/ch04.py`

<a id="seshu-ex-4-8"></a>

### Example 4.8 - Frame: steel cantilever supported by a pinned tie rod

✅ · PDF page 137 · frame, truss

Horizontal steel beam 3 m long (A = 2000 mm², I = 1e6 mm^4) clamped to the wall and supported at its end by a 20 mm rod 5 m long, pinned to the wall 4 m higher; 10 kN at the end. Displacements and rotations of the loaded node and of the tie.

**Model**: feagent model (N, mm): beam 1-2 and rod 3-4 as plane beams; node 4 coincides with node 2 with only equal translations (add_equal_dof), as the book's pin; clamp at 1, pin at 3.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u2 (printed -0.12) [mm] | corrected | -0.056019 | -0.05609 | 0.13 % | ✅ |
| v2 downwards (printed 2.564) [mm] | corrected | 1.282 | 1.2819 | 0.01 % | ✅ |
| theta2 (printed 0.0013) [rad] | corrected | 6.3861e-04 | 6.4096e-04 | 0.37 % | ✅ |
| theta3 (printed 3.26e-4) [rad] | corrected | 1.6242e-04 | 1.6280e-04 | 0.24 % | ✅ |
| theta4 (printed 3.26e-4) [rad] | corrected | 1.6242e-04 | 1.6280e-04 | 0.24 % | ✅ |

**Notes**: Book error: the printed solution (u2 = -0.12 mm, v2 = 2.564 mm, theta2 = 0.0013, theta3 = theta4 = 3.26e-4) is about twice that of the reduced system printed on the same page (Eq. 4.148), which solved gives u2 = -0.0560 mm, v2 = 1.282 mm, theta2 = 6.39e-4, theta3 = theta4 = 1.62e-4 rad; the same follows by hand (vertical stiffness of the node about 7800 N/mm). The comparison is with the solution of Eq. 4.148.

*Code*: `validation/books/seshu/ch04.py`

<a id="seshu-ex-4-9"></a>

### Example 4.9 - Simply supported beam with axial and transverse loads (2 frame elements)

✅ · PDF page 140 · frame, buckling

Simply supported beam compressed by Pa and loaded at midspan by Pt. The linear frame element does not couple axial force and bending: the deflection stays Pt L^3/(48 EI) whatever Pa, while the exact beam-column solution is Pt L^3/(48 EI) 3 (tan b - b)/b^3 with b = sqrt(Pa L^2/(4 EI)).

**Model**: feagent model: 2 and 8 plane beams (EI = AE = 1, L = 2); linear analysis (Model.solve) and second-order analysis with the geometric stiffness (Model.solve_pdelta).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Linear deflection (independent of Pa) | book | 0.16667 | 0.16667 | 0.00 % | ✅ |
| Axial shortening | book | 1 | 1 | 0.00 % | ✅ |
| Second-order deflection, 2 elements | exact | 0.20846 | 0.20839 | 0.03 % | ✅ |
| Second-order deflection, 8 elements | exact | 0.20846 | 0.20846 | 0.00 % | ✅ |

**Notes**: Test values Pa = 0.5 and Pt = 1 (Pa/Pcr = 0.2). feagent's P-Delta analysis adds the coupling missing from the linear element and reproduces the exact solution.

*Code*: `validation/books/seshu/ch04.py`

## Chapter 5

<a id="seshu-ex-5-1"></a>

### Example 5.1 - Thin plate in tension with two CST triangles

✅ · PDF page 183 · plane_stress

Plate 4 x 3 cm, 1 cm thick (E = 2e7 N/cm², nu = 0.3), pulled by 1 N/cm on the right edge. Two CSTs give the exact uniform stress state: u3 = u4 = 2e-7 cm, v2 = v4 = -4.5e-8 cm, eps_x = 5e-8, eps_y = -1.5e-8; some terms of element 1's stiffness matrix are also compared.

**Model**: feagent model: two T3 plane-stress elements (nodes 1-3-2 and 3-4-2), u1 = v1 = u2 = 0, 1.5 N at nodes 3 and 4.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u3 [cm] | book | 2.0000e-07 | 2.0000e-07 | 0.00 % | ✅ |
| u4 [cm] | book | 2.0000e-07 | 2.0000e-07 | 0.00 % | ✅ |
| v2 [cm] | book | -4.5000e-08 | -4.5000e-08 | 0.00 % | ✅ |
| v4 [cm] | book | -4.5000e-08 | -4.5000e-08 | 0.00 % | ✅ |
| v3 [cm] | book | 0 | -4.4316e-23 | 0.00 % | ✅ |
| eps_x | book | 5.0000e-08 | 5.0000e-08 | 0.00 % | ✅ |
| eps_y | book | -1.5000e-08 | -1.5000e-08 | 0.00 % | ✅ |
| Stiffness k(u1,u1), element 1 [1e7 N/cm] | book | 1.34 | 1.337 | 0.22 % | ✅ |
| Stiffness k(v1,v1), element 1 [1e7 N/cm] | book | 1.75 | 1.7537 | 0.21 % | ✅ |
| Stiffness k(u3,u3), element 1 [1e7 N/cm] | book | 0.824 | 0.82418 | 0.02 % | ✅ |
| Stiffness k(v3,v3), element 1 [1e7 N/cm] | book | 0.288 | 0.28846 | 0.16 % | ✅ |
| Stiffness k(u2,u2), element 1 [1e7 N/cm] | book | 0.513 | 0.51282 | 0.03 % | ✅ |
| Stiffness k(v2,v2), element 1 [1e7 N/cm] | book | 1.47 | 1.4652 | 0.33 % | ✅ |

**Notes**: Displacement exponents reconstructed from the page (uncertain OCR).

*Code*: `validation/books/seshu/ch05.py`

<a id="seshu-ex-5-2"></a>

### Example 5.2 - Gauss integration of a polynomial

📐 · PDF page 209 · other

Integral of 2 + x + x^2 over [-1, 1]: the one-point rule gives 4, the two-point rule 4.6667, exact because it integrates polynomials up to degree three exactly.

**Model**: feagent's Gauss-Legendre rules (feagent.solid._gl, the ones used by the elements) with 1 and 2 points.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| 1-point Gauss | book | 4 | 4 | 0.00 % | ✅ |
| 2-point Gauss | book | 4.6667 | 4.6667 | 0.00 % | ✅ |
| Exact integral | exact | 4.6667 | 4.6667 | 0.00 % | ✅ |

*Code*: `validation/books/seshu/ch05.py`

<a id="seshu-ex-5-3"></a>

### Example 5.3 - Gauss integration of cos(pi x/2)

📐 · PDF page 210 · other

Integral of cos(pi x/2) over [-1, 1] (exact 4/pi = 1.273239544) with 1, 2, 3 and 4 Gauss points: 2; 1.232381; 1.274123754; 1.273229508.

**Model**: feagent's Gauss-Legendre rules (feagent.solid._gl) from 1 to 4 points.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| 1-point Gauss | book | 2 | 2 | 0.00 % | ✅ |
| 2-point Gauss | book | 1.2324 | 1.2324 | 0.00 % | ✅ |
| 3-point Gauss | book | 1.2741 | 1.2741 | 0.00 % | ✅ |
| 4-point Gauss | book | 1.2732 | 1.2732 | 0.00 % | ✅ |
| Exact integral | exact | 1.2732 | 1.2732 | 0.00 % | ✅ |

*Code*: `validation/books/seshu/ch05.py`

<a id="seshu-ex-5-4"></a>

### Example 5.4 - The plate of Ex. 5.1 with one bilinear rectangular element

✅ · PDF page 220 · plane_stress

Same plate as Ex. 5.1 with a single bilinear quadrilateral: constant Jacobian diag(2, 1.5) with |J| = 3, 8x8 stiffness matrix (Eq. 5.255) and the same displacements as the two CSTs.

**Model**: feagent model: one PlaneQ4 plane-stress element with 2x2 integration, u1 = v1 = u4 = 0, 1.5 N at nodes 2 and 3.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| J11 | book | 2 | 2 | 0.00 % | ✅ |
| J22 | book | 1.5 | 1.5 | 0.00 % | ✅ |
| |J| | book | 3 | 3 | 0.00 % | ✅ |
| k(u1,u1) [1e7 N/cm] | book | 0.891 | 0.89133 | 0.04 % | ✅ |
| k(u1,v1) [1e7 N/cm] | book | 0.357 | 0.35714 | 0.04 % | ✅ |
| k(v1,v1) [1e7 N/cm] | book | 1.17 | 1.1691 | 0.08 % | ✅ |
| k(u1,u2) [1e7 N/cm] | book | -0.379 | -0.37851 | 0.13 % | ✅ |
| k(u1,u3) [1e7 N/cm] | book | -0.446 | -0.44567 | 0.08 % | ✅ |
| u2 [cm] | book | 2.0000e-07 | 2.0000e-07 | 0.00 % | ✅ |
| u3 [cm] | book | 2.0000e-07 | 2.0000e-07 | 0.00 % | ✅ |
| v2 [cm] | book | 0 | 2.3269e-23 | 0.00 % | ✅ |
| v3 [cm] | book | -4.5000e-08 | -4.5000e-08 | 0.00 % | ✅ |
| v4 [cm] | book | -4.5000e-08 | -4.5000e-08 | 0.00 % | ✅ |

*Code*: `validation/books/seshu/ch05.py`

<a id="seshu-ex-5-5"></a>

### Example 5.5 - Jacobian of a regular and a distorted quadrilateral

📐 · PDF page 222 · plane_stress

Rhombus quadrilateral (-1,0), (0,-1), (1,0), (0,1): |J| = 0.5 at all Gauss points, sum 2 = area. Moving the first node to (0.5, 0) makes the element concave: |J| = -0.0915, 0.125, 0.125, 0.3415 (sum 0.5 = area) and the first Gauss point falls outside the element, at (0.35566, 0).

**Model**: Shape functions and derivatives of feagent's PlaneQ4 (shape, dshape, jacobian) at the 2x2 Gauss points; check that feagent rejects the element whose Jacobian changes sign.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Minimum |J|, regular element | book | 0.5 | 0.5 | 0.00 % | ✅ |
| Sum of |J| = area | book | 2 | 2 | 0.00 % | ✅ |
| Distorted |J| at (-0.577, -0.577) | book | -0.0915 | -0.091506 | 0.01 % | ✅ |
| Distorted |J| at (-0.577, +0.577) | book | 0.125 | 0.125 | 0.00 % | ✅ |
| Distorted |J| at (+0.577, -0.577) | book | 0.125 | 0.125 | 0.00 % | ✅ |
| Distorted |J| at (+0.577, +0.577) | book | 0.3415 | 0.34151 | 0.00 % | ✅ |
| Sum of |J| = area | book | 0.5 | 0.5 | 0.00 % | ✅ |
| x of the first mapped Gauss point | book | 0.35566 | 0.35566 | 0.00 % | ✅ |
| feagent rejects the distorted element (1 = yes) | book | 1 | 1 | 0.00 % | ✅ |

**Notes**: For the distorted element the Jacobian is computed with feagent's Q4 shape functions applied to the distorted coordinates, because feagent (correctly) does not accept the element: Model.add_plane raises a sign-changing Jacobian error.

*Code*: `validation/books/seshu/ch05.py`

<a id="seshu-ex-5-6"></a>

### Example 5.6 + Tabelle 5.3-5.4 - Cantilever modelled with Q4 and Q8 plane elements

✅ · PDF page 225 · plane_stress

Cantilever loaded at the tip by 100 N (theoretical deflection PL^3/(3EI) = 0.02 cm, rotation 0.003): the Q4 is too stiff in bending (parasitic shear) and still 10 % off with 40 elements; the Q8 is within 3 % with two elements.

**Model**: feagent model: 10 x 1 cm plane-stress cantilever, PlaneQ4 meshes (1, 5x1, 5x2, 10x1, 20x1, 40x1) with full integration and PlaneQ8 meshes (1, 2x1) with 2x2 integration; tip shear with consistent forces; rotation from the difference of the tip horizontal displacements.

![seshu-ex-5.6](images/books/seshu-ex-5.6.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Theoretical deflection with EI = 0.1667e7 [cm] | exact | 0.02 | 0.02 | 0.00 % | ✅ |
| Q4 1x1, deflection [cm] | book | 5.1000e-04 | 5.0917e-04 | 0.16 % | ✅ |
| Q4 1x1, rotation | book | 7.6000e-05 | 7.5833e-05 | 0.22 % | ✅ |
| Q4 5x1, deflection [cm] | book | 0.0076 | 0.0076375 | 0.49 % | ✅ |
| Q4 5x1, rotation | book | 0.00114 | 0.0011375 | 0.22 % | ✅ |
| Q4 5x2, deflection [cm] | book | 0.0078 | 0.0078183 | 0.23 % | ✅ |
| Q4 5x2, rotation | book | 0.00117 | 0.0011668 | 0.28 % | ✅ |
| Q4 10x1, deflection [cm] | book | 0.0136 | 0.013578 | 0.16 % | ✅ |
| Q4 10x1, rotation | book | 0.002 | 0.0020222 | 1.11 % | ✅ |
| Q4 20x1, deflection [cm] | book | 0.0168 | 0.016855 | 0.33 % | ✅ |
| Q4 20x1, rotation | book | 0.0025 | 0.0025103 | 0.41 % | ✅ |
| Q4 40x1, deflection [cm] | book | 0.018 | 0.017938 | 0.35 % | ✅ |
| Q4 40x1, rotation | book | 0.0027 | 0.0026716 | 1.05 % | ✅ |
| Q8 1x1, deflection [cm] | book | 0.0188 | 0.018784 | 0.09 % | ✅ |
| Q8 1x1, rotation | book | 0.00287 | 0.002865 | 0.17 % | ✅ |
| Q8 2x1, deflection [cm] | book | 0.0194 | 0.019357 | 0.22 % | ✅ |
| Q8 2x1, rotation | book | 0.0029 | 0.0029229 | 0.79 % | ✅ |

**Notes**: Book error: EI is printed as 0.1667e10 N cm², but with E = 2e7 N/cm² and a 1 x 1 cm section it is 0.1667e7, the only value consistent with 0.02 cm and 0.003. nu is not stated: nu = 0.3 (as in Ex. 5.1 and 5.4) reproduces Table 5.3. The Q8 Table 5.4 is obtained with 2x2 reduced integration (the same as the Q4 of Ex. 5.4); with full 3x3 integration the one-element Q8 gives 0.0153 cm.

*Code*: `validation/books/seshu/ch05.py`

<a id="seshu-ex-5-7"></a>

### Example 5.7 (Fig. 5.42) - Stress concentration around a circular hole

🟡 · PDF page 227 · plane_stress

Plate with a circular hole under uniaxial tension: for the infinite plate the concentration factor at the hole edge is 3. With a 10 x 15 mesh on a quarter the book finds 2.813 with Q4 and 2.795 with Q9 and shows that the Q9 nodal stresses are much more continuous between adjacent elements.

**Model**: feagent model: quarter of a square plate (side 2W = 40a) with a hole of radius a, 10 (radial, geometric progression) x 16 (circumferential) mesh of PlaneQ4 and PlaneQ9, symmetry restraints, uniform tension on the edge x = W (add_solid_surface_load); check with a 20 x 32 Q9 mesh.

![seshu-ex-5.7](images/books/seshu-ex-5.7.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Resultant of the tension (sigma0 W) [N] | book | 20 | 20 | 0.00 % | ✅ |
| Kt at the hole, Q4 10x16 (book 2.813 with its own mesh) | book | - | 3.0105 | - | ✅ |
| Kt at the hole, Q9 10x16 (book 2.795 with its own mesh) | book | - | 3.0853 | - | ✅ |
| Relative sigma_x jump at the second node, Q4 (book 2.12 and 1.70) | book | - | 0.061161 | - | ✅ |
| Relative sigma_x jump at the second node, Q9 (book 1.727 and 1.720) | book | - | 0.020255 | - | ✅ |
| Q9 jump smaller than the Q4 jump (1 = yes) | book | 1 | 1 | 0.00 % | ✅ |
| Kt at the hole, Q9 20x32 (nodal average) | exact | 3 | 3.0444 | 1.48 % | ✅ |

**Notes**: The book gives neither the plate and hole dimensions nor the material nor the mesh grading: the geometry (W = 20a, radial ratio 1.3) is reconstructed by assumption and 16 circumferential divisions instead of 15 put a node on the corner. With this mesh Kt depends strongly on the grading and on how the nodal stress is evaluated (value at the node, Gauss points, extrapolation): the coarse-mesh values are therefore reported without a comparison, with the book value in the label. The quantitative checks are the 20 x 32 Q9 mesh against Kt = 3 (finite width: about +0.3 %) and the book's point on nodal stress continuity: at the second node of the section the jump between adjacent Q9 elements is smaller than for Q4 (here about one third).

*Code*: `validation/books/seshu/ch05.py`

<a id="seshu-sec-5-8-flow"></a>

### Sezione 5.8 (Fig. 5.43-5.46) - Potential flow around a cylinder between walls (stream function)

🟡 · PDF page 230 · potential_flow

Irrotational flow of an ideal fluid around a cylinder between two walls, solved with the stream function. At the top of the cylinder the normalised velocity is 2 for an isolated cylinder; the book finds 1.7878 with Q4 and 2.012 with Q9.

**Model**: feagent model: FieldModel('stream') on the quarter ABCDE (radius a, H = L = 20a), 10 x 16 Q4 and Q9 meshes as in Ex. 5.7, psi = 0 on the cylinder and on the axis, psi = V0 H on the wall, psi = V0 y at the inlet, natural condition on the cylinder section; check with 20 x 32 Q9.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u/V0 at C, Q4 10x16 (book 1.7878 with its own mesh) | book | - | 1.6876 | - | ✅ |
| u/V0 at C, Q9 10x16 (book 2.012 with its own mesh) | book | - | 1.9457 | - | ✅ |
| Q9 closer than Q4 to the exact velocity (1 = yes) | book | 1 | 1 | 0.00 % | ✅ |
| u/V0 at C, Q9 20x32 (nodal average) | exact | 2 | 1.9876 | 0.62 % | ✅ |

**Notes**: Dimensions not given in the book: H = L = 20a assumed, so that wall blockage is negligible (exact velocity about 2.00). With the coarse mesh the value depends strongly on the grading and on where the gradient is evaluated: the values are reported without a comparison, with the book value in the label. The quantitative checks are the 20 x 32 Q9 mesh against 2 and the improvement of Q9 over Q4 (p-refinement).

*Code*: `validation/books/seshu/ch05.py`

## Chapter 6

<a id="seshu-ex-6-1"></a>

### Example 6.1 - Lagrange's equations for a two-mass, three-spring system

📐 · PDF page 251 · spring, dynamics_modal

From the kinetic and potential energies the book derives M = diag(m1, m2) and K = [[k1 + k2, -k2], [-k2, k2 + k3]]. The matrices assembled by feagent and the natural frequencies are compared.

**Model**: feagent model: two nodes with mass (add_nodal_mass) connected to each other and to the ground by three axial springs (add_spring); matrices from assemble_stiffness and assemble_mass.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Max deviation of stiffness matrix [N/m] | book | 0 | 0 | 0.00 % | ✅ |
| Mass of DOF 1 [kg] | book | 2 | 2 | 0.00 % | ✅ |
| Mass of DOF 2 [kg] | book | 1 | 1 | 0.00 % | ✅ |
| First circular frequency [rad/s] | exact | 26.101 | 26.101 | 0.00 % | ✅ |
| Second circular frequency [rad/s] | exact | 50.683 | 50.683 | 0.00 % | ✅ |

**Notes**: Symbolic example: m1 = 2, m2 = 1 kg, k1 = 1000, k2 = 500, k3 = 2000 N/m.

*Code*: `validation/books/seshu/ch06.py`

<a id="seshu-ex-6-2"></a>

### Example 6.2 + Tabella 6.1 - Axial frequencies of a fixed-free bar: lumped and consistent mass

✅ · PDF page 258 · bar, dynamics_modal

Fixed-free steel bar 1 m long: with one element the lumped mass gives 1.414 sqrt(E/rho)/L, the consistent one 1.732, the exact 1.571; with two elements 1.531 and 3.695 (lumped), 1.611 and 5.63 (consistent). Table 6.1 shows the convergence of the first five frequencies: lumped mass underestimates them, consistent mass overestimates them.

**Model**: feagent model: 1, 2, 3, 4, 8 and 16 L2 bars (rho = 7800 kg/m³), modal analysis with lumped (mass='lumped') and consistent (mass='consistent') mass.

![seshu-ex-6.2](images/books/seshu-ex-6.2.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| mode 1, lumped, 1 el. [Hz] | book | 1140 | 1139.7 | 0.02 % | ✅ |
| mode 1, lumped, 2 el. [Hz] | book | 1234 | 1233.6 | 0.03 % | ✅ |
| mode 2, lumped, 2 el. [Hz] | book | 2978 | 2978.3 | 0.01 % | ✅ |
| mode 1, lumped, 3 el. [Hz] | book | 1252 | 1251.5 | 0.04 % | ✅ |
| mode 2, lumped, 3 el. [Hz] | book | 3420 | 3419.2 | 0.02 % | ✅ |
| mode 3, lumped, 3 el. [Hz] | book | 4670 | 4670.7 | 0.02 % | ✅ |
| mode 1, lumped, 4 el. [Hz] | book | 1258 | 1257.8 | 0.02 % | ✅ |
| mode 2, lumped, 4 el. [Hz] | book | 3582 | 3581.9 | 0.00 % | ✅ |
| mode 3, lumped, 4 el. [Hz] | book | 5366 | 5360.7 | 0.10 % | ✅ |
| mode 4, lumped, 4 el. [Hz] | book | 6319 | 6323.4 | 0.07 % | ✅ |
| mode 1, lumped, 8 el. [Hz] | book | 1264 | 1263.9 | 0.01 % | ✅ |
| mode 2, lumped, 8 el. [Hz] | book | 3743 | 3743.1 | 0.00 % | ✅ |
| mode 3, lumped, 8 el. [Hz] | book | 6078 | 6078.5 | 0.01 % | ✅ |
| mode 4, lumped, 8 el. [Hz] | book | 8180 | 8180.2 | 0.00 % | ✅ |
| mode 5, lumped, 8 el. [Hz] | book | 10000 | 9967.7 | 0.32 % | ✅ |
| mode 1, lumped, 16 el. [Hz] | book | 1265 | 1265.4 | 0.03 % | ✅ |
| mode 2, lumped, 16 el. [Hz] | book | 3784 | 3784.1 | 0.00 % | ✅ |
| mode 3, lumped, 16 el. [Hz] | book | 6266 | 6266.3 | 0.00 % | ✅ |
| mode 4, lumped, 16 el. [Hz] | book | 8688 | 8688.1 | 0.00 % | ✅ |
| mode 5, lumped, 16 el. [Hz] | book | 11030 | 11026 | 0.03 % | ✅ |
| mode 1, consistent, 1 el. [Hz] | book | 1396 | 1395.9 | 0.01 % | ✅ |
| mode 1, consistent, 2 el. [Hz] | book | 1299 | 1298.7 | 0.03 % | ✅ |
| mode 2, consistent, 2 el. [Hz] | book | 4537 | 4536.7 | 0.01 % | ✅ |
| mode 1, consistent, 3 el. [Hz] | book | 1280 | 1280.4 | 0.03 % | ✅ |
| mode 2, consistent, 3 el. [Hz] | book | 4188 | 4187.6 | 0.01 % | ✅ |
| mode 3, consistent, 3 el. [Hz] | book | 7597 | 7597 | 0.00 % | ✅ |
| mode 1, consistent, 4 el. [Hz] | book | 1274 | 1274.1 | 0.01 % | ✅ |
| mode 2, consistent, 4 el. [Hz] | book | 4019 | 4019.2 | 0.01 % | ✅ |
| mode 3, consistent, 4 el. [Hz] | book | 7301 | 7301.1 | 0.00 % | ✅ |
| mode 4, consistent, 4 el. [Hz] | book | 10560 | 10558 | 0.02 % | ✅ |
| mode 1, consistent, 8 el. [Hz] | book | 1268 | 1268 | 0.00 % | ✅ |
| mode 2, consistent, 8 el. [Hz] | book | 3853 | 3852.9 | 0.00 % | ✅ |
| mode 3, consistent, 8 el. [Hz] | book | 6586 | 6585.8 | 0.00 % | ✅ |
| mode 4, consistent, 8 el. [Hz] | book | 9563 | 9563.1 | 0.00 % | ✅ |
| mode 5, consistent, 8 el. [Hz] | book | 12850 | 12851 | 0.01 % | ✅ |
| mode 1, consistent, 16 el. [Hz] | book | 1266 | 1266.4 | 0.03 % | ✅ |
| mode 2, consistent, 16 el. [Hz] | book | 3812 | 3811.5 | 0.01 % | ✅ |
| mode 3, consistent, 16 el. [Hz] | book | 6393 | 6393.3 | 0.01 % | ✅ |
| mode 4, consistent, 16 el. [Hz] | book | 9037 | 9036.7 | 0.00 % | ✅ |
| mode 5, consistent, 16 el. [Hz] | book | 11770 | 11767 | 0.03 % | ✅ |
| omega L/sqrt(E/rho), lumped, 1 el., mode 1 | book | 1.414 | 1.4142 | 0.02 % | ✅ |
| omega L/sqrt(E/rho), consistent, 1 el., mode 1 | book | 1.732 | 1.7321 | 0.00 % | ✅ |
| omega L/sqrt(E/rho), lumped, 2 el., mode 1 | book | 1.531 | 1.5307 | 0.02 % | ✅ |
| omega L/sqrt(E/rho), lumped, 2 el., mode 2 | book | 3.695 | 3.6955 | 0.01 % | ✅ |
| omega L/sqrt(E/rho), consistent, 2 el., mode 1 | book | 1.611 | 1.6114 | 0.03 % | ✅ |
| omega L/sqrt(E/rho), consistent, 2 el., mode 2 | book | 5.63 | 5.6293 | 0.01 % | ✅ |
| Exact frequency mode 1 [Hz] | exact | 1265.9 | 1265.9 | 0.00 % | ✅ |
| Exact frequency mode 2 [Hz] | exact | 3797.8 | 3797.8 | 0.00 % | ✅ |
| Exact frequency mode 3 [Hz] | exact | 6329.6 | 6329.6 | 0.00 % | ✅ |
| Exact frequency mode 4 [Hz] | exact | 8861.5 | 8861.5 | 0.00 % | ✅ |
| Exact frequency mode 5 [Hz] | exact | 11393 | 11393 | 0.00 % | ✅ |

**Notes**: Table 6.1 gives frequencies rounded to the Hz, except the fifth lumped-mass mode with 8 elements, printed as 10000 Hz: the computed value is 9967.7 Hz (the same as the closed form of the spring-mass chain), compared to 0.4 %.

*Code*: `validation/books/seshu/ch06.py`

<a id="seshu-ex-6-3"></a>

### Example 6.3 + Tabella 6.2 - Frequencies of a simply supported beam: simple lumped, HRZ and consistent mass

✅ · PDF page 261 · beam, dynamics_modal

Simply supported steel beam 1 m long with I = 100 mm^4: first five bending frequencies with three mass matrices. The simple lumped mass, without rotary inertia, yields fewer modes; HRZ and consistent give all of them. Exact: 14.52, 58.11, 130.75, 232.45, 363.2 Hz.

**Model**: feagent model: 2, 4, 8 and 16 simply supported Euler-Bernoulli beams; modal analysis with lumped (translational only) and consistent mass; HRZ mass built with numpy from feagent's element consistent matrices.

![seshu-ex-6.3](images/books/seshu-ex-6.3.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Exact frequency mode 1 (A = 30 mm²) [Hz] | exact | 14.52 | 14.522 | 0.01 % | ✅ |
| Exact frequency mode 2 (A = 30 mm²) [Hz] | exact | 58.11 | 58.088 | 0.04 % | ✅ |
| Exact frequency mode 3 (A = 30 mm²) [Hz] | exact | 130.75 | 130.7 | 0.04 % | ✅ |
| Exact frequency mode 4 (A = 30 mm²) [Hz] | exact | 232.45 | 232.35 | 0.04 % | ✅ |
| Exact frequency mode 5 (A = 30 mm²) [Hz] | exact | 363.2 | 363.05 | 0.04 % | ✅ |
| mode 1, lumped, 2 el. [Hz] | book | 14.42 | 14.417 | 0.02 % | ✅ |
| mode 1, lumped, 4 el. [Hz] | book | 14.52 | 14.518 | 0.02 % | ✅ |
| mode 2, lumped, 4 el. [Hz] | book | 57.67 | 57.666 | 0.01 % | ✅ |
| mode 3, lumped, 4 el. [Hz] | book | 122.4 | 122.44 | 0.03 % | ✅ |
| mode 1, lumped, 8 el. [Hz] | book | 14.52 | 14.522 | 0.01 % | ✅ |
| mode 2, lumped, 8 el. [Hz] | book | 58.07 | 58.07 | 0.00 % | ✅ |
| mode 3, lumped, 8 el. [Hz] | book | 130.5 | 130.46 | 0.03 % | ✅ |
| mode 4, lumped, 8 el. [Hz] | book | 230.7 | 230.67 | 0.01 % | ✅ |
| mode 5, lumped, 8 el. [Hz] | book | 354.7 | 354.67 | 0.01 % | ✅ |
| mode 1, lumped, 16 el. [Hz] | book | 14.52 | 14.522 | 0.01 % | ✅ |
| mode 2, lumped, 16 el. [Hz] | book | 58.09 | 58.087 | 0.01 % | ✅ |
| mode 3, lumped, 16 el. [Hz] | book | 130.7 | 130.69 | 0.01 % | ✅ |
| mode 4, lumped, 16 el. [Hz] | book | 232.3 | 232.28 | 0.01 % | ✅ |
| mode 5, lumped, 16 el. [Hz] | book | 362.8 | 362.76 | 0.01 % | ✅ |
| mode 1, consistent, 2 el. [Hz] | book | 14.58 | 14.579 | 0.00 % | ✅ |
| mode 2, consistent, 2 el. [Hz] | book | 64.47 | 64.473 | 0.00 % | ✅ |
| mode 3, consistent, 2 el. [Hz] | book | 162.1 | 162.06 | 0.03 % | ✅ |
| mode 4, consistent, 2 el. [Hz] | book | 295.5 | 295.45 | 0.02 % | ✅ |
| mode 1, consistent, 4 el. [Hz] | book | 14.53 | 14.526 | 0.03 % | ✅ |
| mode 2, consistent, 4 el. [Hz] | book | 58.32 | 58.317 | 0.00 % | ✅ |
| mode 3, consistent, 4 el. [Hz] | book | 133.1 | 133.09 | 0.01 % | ✅ |
| mode 4, consistent, 4 el. [Hz] | book | 257.9 | 257.89 | 0.00 % | ✅ |
| mode 5, consistent, 4 el. [Hz] | book | 409.9 | 409.92 | 0.00 % | ✅ |
| mode 1, consistent, 8 el. [Hz] | book | 14.52 | 14.522 | 0.02 % | ✅ |
| mode 2, consistent, 8 el. [Hz] | book | 58.11 | 58.103 | 0.01 % | ✅ |
| mode 3, consistent, 8 el. [Hz] | book | 130.9 | 130.87 | 0.03 % | ✅ |
| mode 4, consistent, 8 el. [Hz] | book | 233.3 | 233.27 | 0.01 % | ✅ |
| mode 5, consistent, 8 el. [Hz] | book | 366.4 | 366.42 | 0.00 % | ✅ |
| mode 1, consistent, 16 el. [Hz] | book | 14.52 | 14.522 | 0.01 % | ✅ |
| mode 2, consistent, 16 el. [Hz] | book | 58.09 | 58.089 | 0.00 % | ✅ |
| mode 3, consistent, 16 el. [Hz] | book | 130.7 | 130.71 | 0.01 % | ✅ |
| mode 4, consistent, 16 el. [Hz] | book | 232.4 | 232.41 | 0.01 % | ✅ |
| mode 5, consistent, 16 el. [Hz] | book | 363.3 | 363.28 | 0.01 % | ✅ |
| mode 1, HRZ, 2 el. [Hz] | book | 14.21 | 14.211 | 0.01 % | ✅ |
| mode 2, HRZ, 2 el. [Hz] | book | 104.3 | 103.96 | 0.33 % | ✅ |
| mode 3, HRZ, 2 el. [Hz] | book | 149.2 | 149.15 | 0.04 % | ✅ |
| mode 4, HRZ, 2 el. [Hz] | book | 180 | 180.06 | 0.04 % | ✅ |
| mode 1, HRZ, 4 el. [Hz] | book | 14.46 | 14.461 | 0.01 % | ✅ |
| mode 2, HRZ, 4 el. [Hz] | book | 56.84 | 56.845 | 0.01 % | ✅ |
| mode 3, HRZ, 4 el. [Hz] | book | 120.2 | 120.24 | 0.03 % | ✅ |
| mode 4, HRZ, 4 el. [Hz] | book | 416.2 | 415.84 | 0.09 % | ✅ |
| mode 5, HRZ, 4 el. [Hz] | book | 481.3 | 481.48 | 0.04 % | ✅ |
| mode 1, HRZ, 8 el. [Hz] | book | 14.51 | 14.507 | 0.02 % | ✅ |
| mode 2, HRZ, 8 el. [Hz] | book | 57.84 | 57.843 | 0.01 % | ✅ |
| mode 3, HRZ, 8 el. [Hz] | book | 129.3 | 129.34 | 0.03 % | ✅ |
| mode 4, HRZ, 8 el. [Hz] | book | 227.4 | 227.38 | 0.01 % | ✅ |
| mode 5, HRZ, 8 el. [Hz] | book | 348 | 348 | 0.00 % | ✅ |
| mode 1, HRZ, 16 el. [Hz] | book | 14.52 | 14.518 | 0.01 % | ✅ |
| mode 2, HRZ, 16 el. [Hz] | book | 58.03 | 58.03 | 0.00 % | ✅ |
| mode 3, HRZ, 16 el. [Hz] | book | 130.4 | 130.4 | 0.00 % | ✅ |
| mode 4, HRZ, 16 el. [Hz] | book | 231.4 | 231.37 | 0.01 % | ✅ |
| mode 5, HRZ, 16 el. [Hz] | book | 360.6 | 360.56 | 0.01 % | ✅ |
| mode 5, standard HRZ (m l^2/78), 64 el. [Hz] | exact | 363.05 | 362.77 | 0.08 % | ✅ |

**Notes**: Three inconsistencies in the book: (1) A is printed as 30 cm², but the frequencies are obtained with A = 30 mm²; (2) the Table 6.2 header lists 2, 3, 4 and 8 elements, but the values correspond to 2, 4, 8 and 16 elements (as shown by the number of lumped-mass modes and by feagent); (3) the HRZ row is obtained with a nodal rotary inertia (m/2) l^2/78 = m l^2/156, half the standard HRZ m l^2/78 stated in the text: the comparison uses this variant, and the standard HRZ is checked separately to converge to the exact values. The 2-element HRZ mode 2 (104.3 printed, 103.96 computed) is compared to 0.5 %.

*Code*: `validation/books/seshu/ch06.py`

<a id="seshu-ex-6-4"></a>

### Example 6.4 - Eigenvalues of the 3-element bar by the generalised Jacobi method

✅ · PDF page 271 · bar, dynamics_modal

The bar of Ex. 6.2 with three elements: K = 1e8 [[0.36, -0.18, 0], ...] N/m and M = [[0.052, 0.013, 0], ...] kg. Generalised Jacobi zeroes the same off-diagonal term of K and M at each rotation and converges to 1280.43, 4187.64 and 7597 Hz with M-orthonormal modes.

**Model**: feagent model: 3 L2 bars, K and consistent M reduced to the free DOFs (assemble_stiffness, assemble_mass_matrix); generalised Jacobi method written with numpy; check against feagent's modal analysis.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Max deviation of K [1e8 N/m] | book | 0 | 3.7253e-17 | 0.00 % | ✅ |
| Max deviation of M [kg] | book | 0 | 2.0817e-17 | 0.00 % | ✅ |
| frequency 1 [Hz] | book | 1280.4 | 1280.4 | 0.00 % | ✅ |
| frequency 2 [Hz] | book | 4187.6 | 4187.6 | 0.00 % | ✅ |
| frequency 3 [Hz] | book | 7597 | 7597 | 0.00 % | ✅ |
| mode 1, component 1 | book | 1.5 | 1.4955 | 0.30 % | ✅ |
| mode 1, component 2 | book | 2.58 | 2.5903 | 0.40 % | ✅ |
| mode 1, component 3 | book | 3.01 | 2.9911 | 0.63 % | ✅ |
| mode 2, component 1 | book | -3.58 | -3.5806 | 0.02 % | ✅ |
| mode 2, component 2 | book | -0.005 | -3.4784e-17 | 100.00 % | ✅ |
| mode 2, component 3 | book | 3.59 | 3.5806 | 0.26 % | ✅ |
| mode 3, component 1 | book | 2.38 | 2.3776 | 0.10 % | ✅ |
| mode 3, component 2 | book | -4.13 | -4.1181 | 0.29 % | ✅ |
| mode 3, component 3 | book | 4.74 | 4.7552 | 0.32 % | ✅ |
| f3 from feagent's modal analysis [Hz] | other | 7597 | 7597 | 0.00 % | ✅ |

**Notes**: The book's modes are printed with three digits: compared to 0.02 in absolute value.

*Code*: `validation/books/seshu/ch06.py`

<a id="seshu-ex-6-5"></a>

### Example 6.5 - Same problem by simultaneous vector iteration

✅ · PDF page 280 · bar, dynamics_modal

The matrices of Ex. 6.4 solved by iterating three trial vectors at once, made M-orthonormal by Cholesky factorisation. After five iterations the book obtains 1280, 4188 and 7597 Hz and modes close to the Jacobi ones.

**Model**: K and consistent M of feagent's 3-bar L2 rod; simultaneous iteration with Cholesky orthonormalisation written with numpy, 5 iterations starting from the identity matrix.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Orthonormalised vector, term (1,1) | book | 4.39 | 4.3853 | 0.11 % | ✅ |
| Orthonormalised vector, term (3,3) | book | 6.66 | 6.6617 | 0.03 % | ✅ |
| First step, term (3,2) [1e-7] | book | 0.343 | 0.34346 | 0.13 % | ✅ |
| frequency 1 [Hz] | book | 1280 | 1280.4 | 0.03 % | ✅ |
| frequency 2 [Hz] | book | 4188 | 4187.8 | 0.00 % | ✅ |
| frequency 3 [Hz] | book | 7597 | 7596.9 | 0.00 % | ✅ |
| mode 1, component 1 | book | 1.5 | 1.496 | 0.27 % | ✅ |
| mode 1, component 2 | book | 2.59 | 2.5903 | 0.01 % | ✅ |
| mode 1, component 3 | book | 2.99 | 2.9906 | 0.02 % | ✅ |
| mode 2, component 1 | book | -3.59 | -3.5956 | 0.16 % | ✅ |
| mode 2, component 2 | book | 0.008 | 0.026761 | 234.51 % | ✅ |
| mode 2, component 3 | book | 3.57 | 3.5504 | 0.55 % | ✅ |
| mode 3, component 1 | book | 2.37 | 2.3546 | 0.65 % | ✅ |
| mode 3, component 2 | book | -4.12 | -4.118 | 0.05 % | ✅ |
| mode 3, component 3 | book | 4.76 | 4.7781 | 0.38 % | ✅ |

**Notes**: After five iterations the vectors are not yet fully converged (the second mode has 0.008 instead of -0.005): the fifth-iteration vectors are compared with the printed ones (0.02 in absolute value), as is the first step (Eqs. 6.191 and 6.193).

*Code*: `validation/books/seshu/ch06.py`

<a id="seshu-ex-6-6"></a>

### Example 6.6 - Free response of a 2-DOF system by mode superposition

✅ · PDF page 286 · spring, dynamics_transient

Symmetric two-mass system with the first mass displaced by 1 and released from rest: frequencies sqrt(k/m) and sqrt(3k/m), x1 = (cos w1 t + cos w2 t)/2, x2 = (cos w1 t - cos w2 t)/2.

**Model**: feagent model: two masses m and three springs k (add_spring, add_nodal_mass); solve_time_history_modal with initial displacement u0 and zero load; check with Newmark direct integration (solve_time_history).

![seshu-ex-6.6](images/books/seshu-ex-6.6.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| omega1 = sqrt(k/m) [rad/s] | exact | 31.623 | 31.623 | 0.00 % | ✅ |
| omega2 = sqrt(3k/m) [rad/s] | exact | 54.772 | 54.772 | 0.00 % | ✅ |
| x1 at t = 0.05 s, mode superposition | exact | -0.46512 | -0.46512 | 0.00 % | ✅ |
| x2 at t = 0.05 s, mode superposition | exact | 0.45478 | 0.45478 | 0.00 % | ✅ |
| x1 at t = 0.1 s, mode superposition | exact | -0.15368 | -0.15368 | 0.00 % | ✅ |
| x2 at t = 0.1 s, mode superposition | exact | -0.8461 | -0.8461 | 0.00 % | ✅ |
| x1 at t = 0.2 s, mode superposition | exact | 0.47902 | 0.47902 | 0.00 % | ✅ |
| x2 at t = 0.2 s, mode superposition | exact | 0.52013 | 0.52013 | 0.00 % | ✅ |
| x1 at t = 0.35 s, mode superposition | exact | 0.51067 | 0.51067 | 0.00 % | ✅ |
| x2 at t = 0.35 s, mode superposition | exact | -0.43834 | -0.43834 | 0.00 % | ✅ |
| x1 at t = 0.35 s, direct Newmark | exact | 0.51067 | 0.51069 | 0.00 % | ✅ |

**Notes**: Symbolic example: k = 1000 N/m, m = 1 kg; response compared at t = 0.05, 0.1, 0.2 and 0.35 s.

*Code*: `validation/books/seshu/ch06.py`

<a id="seshu-ex-6-7"></a>

### Example 6.7 + Tabella 6.3 - Transient temperature in the pin fin (4 elements, trapezoidal rule)

✅ · PDF page 301 · heat_transient

The fin, initially at 30 °C, is connected at t = 0 to the wall at 300 °C. The book integrates the system with the trapezoidal rule and a 1 s step and lists the temperatures of the four free nodes during the first 10 s (Table 6.3); the steady state is the solution of Ex. 4.10.

**Model**: feagent model: the fin of Ex. 4.10 (fin_model) with 4 L2 elements, rho = 2700 kg/m³, c = 900 J/kg°C; FieldModel.solve_transient with theta = 0.5 (Crank-Nicolson) and consistent capacity, dt = 1 s.

![seshu-ex-6.7](images/books/seshu-ex-6.7.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| T2 at t = 1 s [°C] | book | 147.58 | 147.58 | 0.00 % | ✅ |
| T3 at t = 1 s [°C] | book | 39.23 | 39.23 | 0.00 % | ✅ |
| T4 at t = 1 s [°C] | book | 30.73 | 30.729 | 0.00 % | ✅ |
| T5 at t = 1 s [°C] | book | 30.11 | 30.114 | 0.01 % | ✅ |
| T2 at t = 2 s [°C] | book | 166.95 | 166.95 | 0.00 % | ✅ |
| T3 at t = 2 s [°C] | book | 84.25 | 84.251 | 0.00 % | ✅ |
| T4 at t = 2 s [°C] | book | 37.76 | 37.761 | 0.00 % | ✅ |
| T5 at t = 2 s [°C] | book | 31.74 | 31.741 | 0.00 % | ✅ |
| T2 at t = 3 s [°C] | book | 186.34 | 186.34 | 0.00 % | ✅ |
| T3 at t = 3 s [°C] | book | 101.46 | 101.46 | 0.00 % | ✅ |
| T4 at t = 3 s [°C] | book | 57.04 | 57.044 | 0.01 % | ✅ |
| T5 at t = 3 s [°C] | book | 39.91 | 39.914 | 0.01 % | ✅ |
| T2 at t = 4 s [°C] | book | 195.88 | 195.88 | 0.00 % | ✅ |
| T3 at t = 4 s [°C] | book | 118.83 | 118.83 | 0.00 % | ✅ |
| T4 at t = 4 s [°C] | book | 70.77 | 70.772 | 0.00 % | ✅ |
| T5 at t = 4 s [°C] | book | 56.61 | 56.613 | 0.01 % | ✅ |
| T2 at t = 5 s [°C] | book | 204 | 204 | 0.00 % | ✅ |
| T3 at t = 5 s [°C] | book | 130.8 | 130.8 | 0.00 % | ✅ |
| T4 at t = 5 s [°C] | book | 86.35 | 86.354 | 0.00 % | ✅ |
| T5 at t = 5 s [°C] | book | 70.57 | 70.573 | 0.00 % | ✅ |
| T2 at t = 6 s [°C] | book | 209.98 | 209.98 | 0.00 % | ✅ |
| T3 at t = 6 s [°C] | book | 141.96 | 141.96 | 0.00 % | ✅ |
| T4 at t = 6 s [°C] | book | 99.23 | 99.225 | 0.00 % | ✅ |
| T5 at t = 6 s [°C] | book | 85.13 | 85.132 | 0.00 % | ✅ |
| T2 at t = 7 s [°C] | book | 215.3 | 215.3 | 0.00 % | ✅ |
| T3 at t = 7 s [°C] | book | 151.21 | 151.21 | 0.00 % | ✅ |
| T4 at t = 7 s [°C] | book | 111.43 | 111.43 | 0.00 % | ✅ |
| T5 at t = 7 s [°C] | book | 97.73 | 97.729 | 0.00 % | ✅ |
| T2 at t = 8 s [°C] | book | 219.81 | 219.81 | 0.00 % | ✅ |
| T3 at t = 8 s [°C] | book | 159.6 | 159.6 | 0.00 % | ✅ |
| T4 at t = 8 s [°C] | book | 122.06 | 122.06 | 0.00 % | ✅ |
| T5 at t = 8 s [°C] | book | 109.4 | 109.4 | 0.00 % | ✅ |
| T2 at t = 9 s [°C] | book | 223.84 | 223.84 | 0.00 % | ✅ |
| T3 at t = 9 s [°C] | book | 166.93 | 166.93 | 0.00 % | ✅ |
| T4 at t = 9 s [°C] | book | 131.69 | 131.69 | 0.00 % | ✅ |
| T5 at t = 9 s [°C] | book | 119.69 | 119.69 | 0.00 % | ✅ |
| T2 at t = 10 s [°C] | book | 227.39 | 227.39 | 0.00 % | ✅ |
| T3 at t = 10 s [°C] | book | 173.51 | 173.51 | 0.00 % | ✅ |
| T4 at t = 10 s [°C] | book | 140.2 | 140.2 | 0.00 % | ✅ |
| T5 at t = 10 s [°C] | book | 128.95 | 128.95 | 0.00 % | ✅ |
| Steady-state T5 (Ex. 4.10) [°C] | book | 204.63 | 204.63 | 0.00 % | ✅ |

**Notes**: The table temperatures are printed with two decimals: compared to 0.006 °C.

*Code*: `validation/books/seshu/ch06.py`

## Chapter 7

<a id="seshu-sec-7-1-crankshaft"></a>

### Sezione 7.1 (Tabella 7.1) - Torsional vibrations of a crankshaft: beams versus 3D model

⛔ · PDF page 305 · frame, dynamics_modal

Study of the torsional frequencies of an engine crankshaft in five configurations (free, on bearings, with flywheel and pulley, bearings as springs, reciprocating masses), comparing a beam model with a solid model (Table 7.1).

**Model**: In the book: 3D 6-DOF beams with spring-damper supports and lumped masses, compared with a solid model of 8260 tetrahedra. feagent would need 3D beams, springs (add_spring), nodal masses and tetrahedra: all available.

**Notes**: Not reproducible: the book gives neither the shaft geometry nor the section properties, the flywheel and pulley masses or the bearing stiffnesses; it only lists the frequencies of Table 7.1. The feagent capabilities required are all available.

*Code*: `validation/books/seshu/ch07.py`

<a id="seshu-sec-7-2-vessel"></a>

### Sezione 7.2 (Fig. 7.13-7.16) - Pressure vessel with threaded closures (axisymmetric)

⛔ · PDF page 313 · axisym

Stress analysis of a high-pressure vessel (about 2000 bar) closed by threaded plugs, modelled as a solid of revolution; the book shows stress maps in the threads (Fig. 7.16).

**Model**: In the book: about 5000 axisymmetric triangles and 2750 nodes, thread treated as axisymmetric. In feagent: T3/T6 plane elements with formulation='axisym' and pressure on the sides.

**Notes**: Not reproducible: dimensions and material are not given (Fig. 7.13 has no usable dimensions) and the results are colour maps only, with no numerical values to compare. feagent has the required axisymmetric elements.

*Code*: `validation/books/seshu/ch07.py`
