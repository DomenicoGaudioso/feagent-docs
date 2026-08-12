---
layout: default
title: "11 - Excel I/O"
parent: English
nav_order: 11
---

# 11 - Excel I/O

beamfeapy supports full model import/export via Excel files (.xlsx). This is convenient for integration with other tools or for defining models in a tabular way.

## Installation

```bash
pip install beamfeapy[excel]
```

## Generate a template

```python
from beamfeapy.io_excel import write_template
write_template("input.xlsx")    # generate a fillable Excel file
```

The template contains sheets for all structural data types.

## Import a model from Excel

```python
from beamfeapy import Model, read_excel

# Method 1
m = read_excel("input.xlsx")

# Method 2 (equivalent)
m = Model.from_excel("input.xlsx")

res = m.solve()
```

## Save a model to Excel

```python
m.to_excel("model.xlsx")
mx = Model.from_excel("model.xlsx")
```

The workbook is written in the same canonical sheet format used by
`Model.from_excel`, so it can be used as a persistent model file or as an
exchange file for the Streamlit app.

## Export results to Excel

```python
res.to_excel("results.xlsx", n_diagram=21)
```

The file contains sheets:
- **Displacements**: nodal displacements for each node
- **Reactions**: support reactions
- **ElementEndForces**: end forces for each element
- **InternalForces**: internal force diagrams (if `n_diagram > 0`)

## Client tabulated report

```python
res = m.solve(cases={"G": 1.35, "Q": 1.5})
res.to_client_excel("client_tables.xlsx", n_diagram=41)
```

The client workbook contains the model, assigned loads, nodal displacements,
reactions, element end forces and internal force tables in one file.

## Import from external Excel tables

```python
from beamfeapy import Model, write_normalized_external_excel

write_normalized_external_excel("sap_tables.xlsx", "beamfeapy_input.xlsx")
m = Model.from_external_excel("sap_tables.xlsx", A=0.01, Iy=2e-5, Iz=3e-5, J=1e-5)
```

The external importer recognizes common SAP2000, MIDAS and Robot-like sheet and
column names such as `Joint Coordinates`, `Connectivity - Frame`,
`Joint Restraint Assignments`, `Joint Loads - Force`, `NODE`, `ELEMENT`,
`CONSTRAINT`, `CONLOAD`, `Nodes`, `Bars` and `Supports`. If the external file
does not include full material or section numeric properties, pass defaults
(`E`, `nu`, `A`, `Iy`, `Iz`, `J`) and review the normalized workbook before use.

## Excel sheet format

| Sheet | Main columns |
|-------|-------------|
| Node | Node, X, Y, Z |
| Material | Material, E, nu, [alpha], [G], [rho] |
| Section | Section, A, Iy, Iz, J, [Asy], [Asz], [h_y], [h_z] |
| Element | Element, NodeI, NodeJ, Material, Section, [shear], [RefX, RefY, RefZ], [ReleasesI], [ReleasesJ] |
| Support | Node, Dx, Dy, Dz, Rx, Ry, Rz (1 = restrained) |
| NodalLoad | Node, Fx, Fy, Fz, Mx, My, Mz, [Case] |
| DistributedLoad | Element, Component, qi, [qj], [a], [b], [frame], [Case] |
| ConcentratedLoad | Element, xi, Fx, Fy, Fz, Mx, My, Mz, [frame], [Case] |
| Thermal | Element, [dT_axial], [dT_grad_y], [dT_grad_z], [Case] |
| Settlement | Node, Dof, Value |
| Prestress | Element, P, [e_i], [e_j], [plane], [sag], [Case] |

Sheet and column names are recognized case-insensitively.
