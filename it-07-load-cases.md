---
layout: default
title: "07 - Load Case e Combinazioni"
parent: Italiano
nav_order: 7
---

# 07 - Load Case e Combinazioni

Ogni carico può essere assegnato a un **load case** tramite il parametro `case`. Questo permette di risolvere combinazioni di carichi per sovrapposizione.

## Assegnazione ai casi

```python
# Caso G: carichi permanenti
m.add_distributed_load(2, "fy", -20e3, case="G")
m.add_nodal_load(2, Fx=30e3, case="Q")               # caso Q: variabili
m.add_thermal_load(1, dT_axial=15, case="T")            # caso T: termico
```

## Elencare e risolvere i casi

```python
m.load_cases()                     # → ['G', 'Q', 'T']

# Singolo caso
res_G = m.solve(cases="G")

# Combinazione
res_GQ = m.solve(cases=["G", "Q"])

# Tutti i carichi (default)
res_all = m.solve()
```

## Superposizione lineare

Per il principio di sovrapposizione:

```python
# res_GQ == res_G + res_Q (a meno dell'errore numerico)
```

I risultati (spostamenti, reazioni, forze interne) si sommano linearmente per casi diversi. Questo è valido per analisi elastica lineare.