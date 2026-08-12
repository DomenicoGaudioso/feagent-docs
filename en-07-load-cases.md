---
layout: default
title: "07 - Load Cases & Combinations"
parent: English
nav_order: 7
---

# 07 - Load Cases and Combinations

Each load can be assigned to a **load case** via the `case` parameter. This allows solving load combinations by superposition.

## Assigning to cases

```python
# Case G: permanent loads
m.add_distributed_load(2, "fy", -20e3, case="G")
m.add_nodal_load(2, Fx=30e3, case="Q")               # case Q: variable
m.add_thermal_load(1, dT_axial=15, case="T")            # case T: thermal
```

## Listing and solving cases

```python
m.load_cases()                     # → ['G', 'Q', 'T']

# Single case
res_G = m.solve(cases="G")

# Combination
res_GQ = m.solve(cases=["G", "Q"])

# All loads (default)
res_all = m.solve()
```

## Linear superposition

By the superposition principle:

```python
# res_GQ == res_G + res_Q (up to numerical error)
```

Results (displacements, reactions, internal forces) add linearly for different cases. This is valid for linear elastic analysis.

## Combinations with multiplicative coefficients

`Model.solve` accepts a **dictionary `{case: coefficient}`** as `cases` to combine load patterns with factors (e.g. ULS per NTC/Eurocode):

```python
res = m.solve(cases={"G": 1.35, "Q": 1.5})        # 1.35·G + 1.5·Q
res = m.solve(cases={"G": 1.0, "Q": 0.3, "N": 1.0})  # any combination
```

Displacements, reactions and **internal forces** respect the coefficients (linearity verified). `cases` remains usable as string or list (coeff 1).
