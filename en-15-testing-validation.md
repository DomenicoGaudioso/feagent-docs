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
| `test_vs_opensees.py` | Cross-validation with OpenSeesPy (skipped if unavailable) |
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

- **OpenSeesPy**: node-by-node displacement comparison on 3D models
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
python validation/validate_opensees_3d.py       # 3D OpenSees cross-validation
```

## Performance benchmark

```bash
python benchmark/benchmark.py   # dense/sparse/PyNite timing comparison
```
