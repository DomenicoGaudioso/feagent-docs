---
layout: default
title: "14 - Usage Examples"
parent: English
nav_order: 14
has_children: true
---

# 14 - Usage Examples

The `usage_examples/` directory contains **24 self-contained scripts** covering every beamfeapy feature. For the gallery with rendered charts see [Case Studies](en-16-case-studies-gallery.html). (The `examples/` directory contains a shorter series `ex01..ex10` with the same concepts.)

## Example Index

| # | File | Description |
|---|------|-------------|
| 01 | `01_cantilever_nodal_load.py` | Cantilever with tip load — hello world |
| 02 | `02_simply_supported_beam.py` | Simply supported beam with uniform distributed load |
| 03 | `03_distributed_loads.py` | Partial and trapezoidal distributed loads |
| 04 | `04_concentrated_loads_in_span.py` | Concentrated forces and moments at internal points |
| 05 | `05_thermal_loads.py` | Uniform + gradient thermal loads on 3D portal |
| 06 | `06_thermal_profile.py` | Nonlinear thermal profile (eigenstress, EN 1991-1-5) |
| 07 | `07_settlements.py` | Imposed nodal settlements |
| 08 | `08_prestress_parabolic.py` | Parabolic prestress (equivalent load method) |
| 09 | `09_prestress_cable_geometry.py` | Prestress from 3D cable geometry |
| 10 | `10_timoshenko.py` | Timoshenko vs Euler-Bernoulli |
| 11 | `11_end_releases.py` | End releases (hinges) |
| 12 | `12_tapered_beam.py` | Tapered element (1 single element) |
| 13 | `13_tapered_beam_stations.py` | Tapered with section IDs and stations |
| 14 | `14_3d_portal_frame.py` | 3D portal frame with multiple loads |
| 15 | `15_load_cases.py` | Load cases and combinations |
| 16 | `16_ref_vector_and_roll.py` | Section orientation (ref_vector and roll) |
| 17 | `17_support_types.py` | Support types: fix, pin, roller, custom |
| 18 | `18_internal_forces.py` | Post-processing: internal forces and deformed shape |
| 19 | `19_plotting.py` | All Plotly visualization functions |
| 20 | `20_excel_io.py` | Excel import/export |
| 21 | `21_sparse_solver.py` | Dense vs sparse solver comparison |
| 22 | `22_continuous_beam.py` | Multi-span continuous beam |
| 23 | `23_frame_with_hinges.py` | Frame with internal hinges |
| 24 | `24_prestress_secondary_moments.py` | Secondary moments in indeterminate structure |
| 25 | `25_modal_analysis.py` | Modal analysis (masses from load cases) + combinations with coefficients |

## Running

```bash
python usage_examples/01_cantilever_nodal_load.py
```

HTML output (Plotly charts) is saved to `usage_examples/output/`.

## Dependencies for charts and Excel

```bash
pip install beamfeapy[plot]     # Plotly charts (plotly + kaleido)
pip install beamfeapy[excel]    # Excel import/export (pandas + openpyxl)
pip install beamfeapy[all]      # everything
```
