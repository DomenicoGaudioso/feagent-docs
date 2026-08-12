---
layout: default
title: "01 - Installation"
parent: English
nav_order: 1
---

# 01 - Installation

## Requirements

- Python ≥ 3.9
- numpy ≥ 1.24
- scipy ≥ 1.10

## Installation

### From source (development)

```bash
git clone https://github.com/DomenicoGaudioso/beamfeapy.git
cd beamfeapy
pip install -e ".[all]"
```

### Base dependencies only (numpy + scipy)

```bash
pip install -e .
```

## Extras

| Extra | Packages | Description |
|-------|----------|-------------|
| `plot` | plotly, kaleido | Interactive Plotly charts |
| `excel` | pandas, openpyxl | Excel import/export |
| `all` | plotly, kaleido, pandas, openpyxl | Everything |
| `dev` | plotly, kaleido, pandas, openpyxl, pytest | Development + tests |

Example:

```bash
pip install -e ".[all]"       # everything
pip install -e ".[plot]"      # charts only
pip install -e ".[excel]"     # Excel only
```

## Verify installation

```python
import beamfeapy
print(beamfeapy.__version__)  # 0.4.1
```

## Running tests

```bash
pip install -e ".[dev]"
python -m pytest tests -q
```

## Troubleshooting

### ImportError: plotly / pandas not found

The `plot` or `excel` extras are not installed. Run:

```bash
pip install -e ".[all]"
```

### ValueError: ref_vector parallel to beam axis

For vertical elements (Z direction), do not use `ref_vector=(0,0,1)` which is parallel to the axis. Use the default (which automatically picks Y) or `ref_vector=(1,0,0)` for a different orientation.
