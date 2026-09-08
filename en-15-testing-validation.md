---
layout: default
title: "15 - Testing & Validation"
parent: English
nav_order: 15
---

# 15 - Testing and Validation

## Automated tests

The project includes a comprehensive test suite that verifies results against analytical solutions and other FEM solvers.

### Running

```bash
python -m pytest tests/ -v
```

### Test structure

| File | Content |
|------|---------|
| `test_beam.py` | Original tests: cantilever, simply supported, thermal, settlements, Timoshenko, concentrated loads, prestress, distributed loads |
| `test_tapered.py` | Tapered element: exact stiffness, comparison with fine mesh, thermal, Timoshenko tapered, distributed loads |
| `test_io_excel.py` | Excel import/export |
| `test_loadcases_plots.py` | Load cases and smoke tests for plot functions |
| `test_analytical_2d.py` | 2D analytical solutions: cantilever, simply supported, fixed-fixed, triangular loads, superposition |
| `test_analytical_3d.py` | 3D analytical solutions: torsion, biaxial, inclined elements, thermal, prestress |
| `test_crossvalid_ext.py` | Cross-validation with an external FEM solver (skipped if unavailable) |
| `test_vs_pynite.py` | Cross-validation with PyNite (skipped if unavailable) |
| `test_vs_anastruct.py` | Cross-validation with anastruct (2D) |
| `test_solver_consistency.py` | Sparse = dense, global equilibrium, tapered vs prismatic, Timoshenko |

## Verification types

### Exact analytical solutions

- Simply supported beam: `5qL⁴/384EI`
- Cantilever: `PL³/3EI`, `PL²/2EI`, `PL/EA`
- Torsion: `TL/GJ`
- Thermal expansion: `α·ΔT·L`
- Restrained bar: `N = -EA·α·ΔT`

### Cross-validation with other solvers

- **solutore esterno (openseespy)**: node-by-node displacement comparison on 3D models
- **PyNite**: comparison on single and multi-element cantilevers
- **anastruct**: comparison on 2D models (simply supported, cantilever, continuous beam)

### Internal consistency

- Sparse solver = dense solver (within numerical tolerance)
- Global equilibrium (reactions = applied loads)
- Tapered element with constant section = prismatic element
- Section by ID = direct section

## validation/ directory

Extended validation scripts that generate charts:

```bash
python validation/validate.py                    # analytical validation
python validation/validate_timoshenko.py        # Timoshenko vs EB
python validation/validate_releases.py          # releases (hinges)
python validation/validate_thermal_profile.py  # eigenstress thermal profile
python validation/validate_ext_3d.py            # 3D cross-validation (external solver)
python validation/simis_benchmarks.py           # published benchmarks (Bell, Irgens, MacNeal-Harder, NAFEMS, OpenSees)
```

## Published benchmarks (simis.io / Ashes, NAFEMS)

`validation/simis_benchmarks.py` reproduces the structural benchmarks of the Ashes
regression suite (<https://www.simis.io/docs/validation-benchmarks-benchmarking-tests>)
that apply to a 3D frame solver, plus a NAFEMS classic. Every case compares a feagent
result with the published reference using the tolerance of the original test, and the
same cases run under pytest in `tests/test_benchmarks_simis.py`.

| Group | Reference | What is checked | Tolerance |
|---|---|---|---|
| Bell (1987) cantilever | HE300B, l/h = 2, 5, 10, 20 | Euler-Bernoulli and Timoshenko tip deflection, ratio w_T/w_E | 0.5 % |
| Bell (1987) fig. 6.6 | fixed end + two supports | deflection under the load, moments at the fixed end / load / support, with and without shear | 1 % |
| Irgens (1985) | ex. 1, 3 (ch. 19), ex. 5 (ch. 24) | cantilever deflection and rotation, simply supported beam under q, unsymmetric angle section (biaxial bending, principal axes via `axes`) | 1 % |
| MacNeal & Harder (1985) | straight, curved, twisted beam | extension, in-plane / out-of-plane shear, twist; curved beam with 6 and 24 chords; twisted beam with 12, 48, 91 elements (`roll`) | 1-2 % (twist 7 %) |
| Static pull, Maximum stress | one-element cantilever, 17 + 2 cases | displacements, twist, Navier stresses (`beam_stresses`), rotated box section | 0.1 % |
| Element weight, Spring test | self-weight, 6-DOF springs | reactions from `add_self_weight` (also with g of Mars), `P/K` on elastic supports | 0.01 % |
| Eigenfrequency cylinder | cantilever tube, 22 elements | first frequency with lumped and consistent mass | 0.1 % |
| Decay test tower | 87.6 m box tower, 100 elements | periods (Biggs 1964) and free decay from a modal initial velocity with 10 damping models (mass, stiffness, Rayleigh) against the analytical damped oscillator | 1 % |
| Earthquake / Static pull OpenSees | 100 m tubular tower, 150 t top mass | base-acceleration (constant and sinusoidal) and top-force (ramp, hold, release) time histories against OpenSees run locally (`OpenSees.exe`): top displacement and acceleration, base shear and moment | 0.5 % / 5 % |
| NAFEMS FV2 | pin-ended cross | first 8 in-plane frequencies (11.336, 17.709 x3, 45.345, 57.390 x3 Hz) | 1 % |
| NAFEMS FV4 | cantilever with off-centre point masses | 6 coupled flexural-torsional frequencies with close eigenvalues (1.723, 1.727, 7.413, 9.972, 18.155, 26.957 Hz); masses on rigid links | 1 % |
| NAFEMS FV5 | deep simply-supported beam | 9 frequencies (flexural, torsional, extensional) with Timoshenko shear and rotary inertia (`mass="consistent-rotary"`) | 2 % |
| Nonlinear solver | isolators and dampers vs OpenSees | bilinear, friction pendulum, coupled bidirectional isolator, nonlinear viscous damper under EC8-compatible records; energy balance of an isolated deck — see [40 - Nonlinear time history](en-40-nonlinear-time-history.html) | 0.5-1 % |

The full table of results is written to `validation/output/simis_benchmarks.md` and published as
[39 - Benchmark report](en-39-benchmark-report.html). Two details worth
knowing when reading it: the MacNeal twist reference depends on the torsional constant adopted
(feagent uses Roark's exact value for the 2:1 rectangle, so the gap is 6 %, inside the 7 % allowed
by Ashes for the same reason), and for a base acceleration applied as a step at t = 0 OpenSees
starts with zero acceleration while feagent derives it from the load, which leaves a 0.1 % RMS
difference on an otherwise identical response.

## Performance benchmark

```bash
python benchmark/benchmark.py   # dense/sparse/PyNite timing comparison
```
