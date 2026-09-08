---
layout: default
title: "11 - Excel I/O"
parent: English
nav_order: 11
---

# 11 - Excel I/O

feagent reads and writes complete models as Excel workbooks (`.xlsx`): one
sheet per data type, one row per node, element, support or load. This is the
format used by the [command-line interface](en-37-cli.html) and by the
[web UI](en-28-web-ui.html), and a convenient way to exchange models with other
tools. For a guided walk through every sheet see the
[CLI tutorial](en-41-cli-excel-tutorial.html).

## Installation

```bash
pip install "feagent[excel]"     # pandas + openpyxl
```

## Generate a template

```python
from feagent.io_excel import write_template

write_template("input.xlsx")                    # 2-element cantilever, one load of every type
write_template("blank.xlsx", blank=True)        # column headers only
write_template("bare.xlsx", readme=False, combinations=False)
```

The template contains every sheet of the format, a `README` sheet with the
description of each column (units, conventions, in English and Italian) and a
`Combination` sheet with SLU/SLE examples. From the terminal:
`feagent template input.xlsx [--blank] [--no-readme] [--no-combos]`.

## Import a model from Excel

```python
from feagent import Model, read_excel

m = read_excel("input.xlsx")            # method 1
m = Model.from_excel("input.xlsx")      # method 2 (equivalent)
res = m.solve(cases={"G": 1.35, "Q": 1.5})
```

Only the `Node` sheet is mandatory; missing or empty sheets are ignored, as
are `README` and any sheet whose name is not recognized. Sheet and column
names are matched case-insensitively. Material and section **names** are
kept on the objects (`material.name`, `section.name`, and `m.sections[name]`),
so they survive a save/reload cycle.

## Validate a workbook

```python
from feagent.validate import validate_excel

rep = validate_excel("input.xlsx", solve=True)
rep.ok                       # False if there are errors
for f in rep.findings:       # code, level, sheet, Excel row, message
    print(f)
```

`validate_excel` runs the same checks as `feagent check`: missing sheets or
columns, duplicated ids, references to undefined nodes/materials/sections,
zero-length elements, missing supports, loads outside the element, thermal
gradients without depth, combinations citing unknown load cases; with
`solve=True` also a trial analysis with a conditioning test of the stiffness
matrix and a global equilibrium check. See the code table in
[37 - CLI, `check`](en-37-cli.html#check---validate-the-workbook).

## Load combinations: the `Combination` sheet

Named combinations can be stored in the workbook, one row per
(combination, load case) pair:

| Name | Case | Coef |
|---|---|---|
| SLU | G | 1.35 |
| SLU | Q | 1.5 |
| SLE_rara | G | 1.0 |
| SLE_rara | Q | 1.0 |

```python
from feagent.io_excel import read_combinations

combos = read_combinations("input.xlsx")      # {"SLU": {"G": 1.35, "Q": 1.5}, "SLE_rara": {...}}
results = m.solve_many(combos)                # one factorization for all
env = m.envelope(combos)                      # feagent.combinations.Envelope
```

Column aliases: `Combination`/`Combo` for `Name`, `LoadCase` for `Case`,
`Coefficient`/`Factor`/`gamma` for `Coef` (empty = 1). The sheet may also be
called `Combinations`, `Combo` or `Combos`. The CLI uses it with
`feagent solve --combos` and `feagent report --combos`.

## Save a model to Excel

```python
m.to_excel("model.xlsx")
m.to_excel("model.xlsx", combinations={"SLU": {"G": 1.35, "Q": 1.5}})
mx = Model.from_excel("model.xlsx")
```

The workbook is written in the same canonical sheet format read by
`Model.from_excel`, so it can be used as a persistent model file or as an
exchange file for the Streamlit app. Timoshenko flags (`shear`), shear areas
(`Asy`, `Asz`), end releases, reference vectors, material and section names
are all preserved. Prestress tendons are written to the `Prestress` sheet
only: the equivalent nodal and distributed loads they generate are **not**
duplicated in the load sheets, because reading the `Prestress` sheet
regenerates them.

## Export results to Excel

```python
res.to_excel("results.xlsx", n_diagram=21)
```

Sheets:

- **Displacements**: nodal displacements `ux, uy, uz, rx, ry, rz` (global axes)
- **Reactions**: support reactions `Fx .. Mz` (global axes)
- **ElementEndForces**: end forces of each element `FxI .. MzJ` (local axes)
- **InternalForces**: `N, Vy, Vz, T, My, Mz` at `n_diagram` stations per element (if `n_diagram > 0`)

## Read results back

```python
from feagent import read_results_excel

data = read_results_excel("results.xlsx")
data["displacements"][3]      # array(6,) of node 3
data["reactions"][1]          # array(6,) of node 1
data["element_forces"][2]     # array(12,) of element 2
data["internal_forces"]       # DataFrame of the diagrams (if present)
```

From the terminal: `feagent results results.xlsx --node 3 --element 2`.

> To store **modal** and **buckling** results as well, and for large models,
> use HDF5: see [22 - Saving Results](en-22-saving-results.html).

## Envelope workbook

```python
from feagent.io_excel import write_envelope

env = m.envelope(read_combinations("input.xlsx"))
write_envelope(env, "envelope.xlsx", n_diagram=21)
```

Sheets `Summary` (combinations and coefficients), `Displacements` and
`Reactions` (min/max per node and DOF with the governing combination;
reactions for restrained nodes only) and `InternalForces` (min/max of each
component along each element with the governing combination and its
position). This is the file written by `feagent solve --combos --envelope`.

## Client tabulated report

```python
res = m.solve(cases={"G": 1.35, "Q": 1.5})
res.to_client_excel("client_tables.xlsx", n_diagram=41)
```

The client workbook contains the model, the assigned loads, nodal
displacements, reactions, element end forces and internal-force tables in one
file (`feagent solve --client`).

## Import from external Excel tables

```python
from feagent import Model, write_normalized_external_excel

write_normalized_external_excel("sap_tables.xlsx", "feagent_input.xlsx")
m = Model.from_external_excel("sap_tables.xlsx", A=0.01, Iy=2e-5, Iz=3e-5, J=1e-5)
```

The external importer recognizes common SAP2000, MIDAS and Robot-like sheet
and column names such as `Joint Coordinates`, `Connectivity - Frame`,
`Joint Restraint Assignments`, `Joint Loads - Force`, `NODE`, `ELEMENT`,
`CONSTRAINT`, `CONLOAD`, `Nodes`, `Bars` and `Supports`. If the external file
does not include full material or section numeric properties, pass defaults
(`E`, `nu`, `A`, `Iy`, `Iz`, `J`) and review the normalized workbook before
use (`feagent convert sap_tables.xlsx model.xlsx --A ... --Iy ...`).

## Excel sheet format

| Sheet | Columns (optional ones in brackets) |
|-------|-------------|
| Node | Node, X, Y, Z |
| Material | Material, E, [nu], [alpha], [G], [rho] |
| Section | Section, A, Iy, Iz, J, [Asy], [Asz] |
| Element | Element, NodeI, NodeJ, Material, Section, [shear], [RefX, RefY, RefZ], [ReleasesI], [ReleasesJ] |
| Support | Node, Dx, Dy, Dz, Rx, Ry, Rz (1 = restrained, global axes) |
| NodalLoad | Node, Fx, Fy, Fz, Mx, My, Mz, [Case] |
| DistributedLoad | Element, Component, qi, [qj], [a], [b], [frame], [Case] - `a`, `b` normalized in [0, 1] |
| ConcentratedLoad | Element, xi, Fx, Fy, Fz, Mx, My, Mz, [frame], [Case] - `xi` normalized in [0, 1] |
| Thermal | Element, [dT_axial], [dT_grad_y], [h_y], [dT_grad_z], [h_z], [Case] |
| Settlement | Node, Dof, Value (always active, no load case) |
| Prestress | Element, P, [e_i], [e_j], [plane], [sag], [Case] |
| Combination | Name, Case, Coef (optional, see above) |
| README | free text, ignored |

Units are SI (N, m, Pa, kg, K) or any consistent system. Gravity loads on
horizontal beams are `fy` components with a negative sign (local `y` = global
`Y` by default); see [13 - Conventions](en-13-conventions.html) and
[08 - Section Orientation](en-08-section-orientation.html). The programmatic
description of every column (`feagent.io_excel.SHEET_DOCS`) is what the
template writes in its `README` sheet.
