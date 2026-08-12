---
layout: default
title: "11 - Excel I/O"
parent: Italiano
nav_order: 11
---

# 11 - Excel I/O

beamfeapy supporta l'importazione ed esportazione completa del modello tramite file Excel (.xlsx). Questo è comodo per integrarsi con altri tool o per definire modelli in modo tabellare.

## Installazione

```bash
pip install beamfeapy[excel]
```

## Generare un template

```python
from beamfeapy.io_excel import write_template
write_template("input.xlsx")    # genera un file Excel compilabile
```

Il template contiene fogli con tutti i tipi di dati strutturali.

## Importare un modello da Excel

```python
from beamfeapy import Model, read_excel

# Metodo 1
m = read_excel("input.xlsx")

# Metodo 2 (equivalente)
m = Model.from_excel("input.xlsx")

res = m.solve()
```

## Esportare i risultati su Excel

```python
res.to_excel("risultati.xlsx", n_diagram=21)
```

Il file contiene i fogli:
- **Displacements**: spostamenti nodali per ogni nodo
- **Reactions**: reazioni vincolari
- **ElementEndForces**: forze d'estremità per ogni elemento
- **InternalForces**: diagrammi delle azioni interne (se `n_diagram > 0`)

## Rileggere i risultati da Excel

```python
from beamfeapy import read_results_excel

data = read_results_excel("risultati.xlsx")
data["displacements"][3]      # array(6,) spostamenti nodo 3
data["reactions"][1]          # array(6,) reazioni nodo 1
data["element_forces"][2]     # array(12,) forze d'estremità elemento 2
data["internal_forces"]       # DataFrame dei diagrammi (se presente)
```

> Per salvare anche risultati **modali** e di **buckling**, e per modelli grandi,
> usa il formato HDF5: vedi [22 - Salvataggio risultati (Excel e HDF5)](it-22-saving-results.html).

## Formato dei fogli Excel

| Foglio | Colonne principali |
|--------|-------------------|
| Node | Node, X, Y, Z |
| Material | Material, E, nu, [alpha], [G], [rho] |
| Section | Section, A, Iy, Iz, J, [Asy], [Asz] |
| Element | Element, NodeI, NodeJ, Material, Section, [shear], [RefX, RefY, RefZ], [ReleasesI], [ReleasesJ] |
| Support | Node, Dx, Dy, Dz, Rx, Ry, Rz (1 = vincolato) |
| NodalLoad | Node, Fx, Fy, Fz, Mx, My, Mz, [Case] |
| DistributedLoad | Element, Component, qi, [qj], [a], [b], [frame], [Case] — a,b normalizzati [0,1] |
| ConcentratedLoad | Element, xi, Fx, Fy, Fz, Mx, My, Mz, [frame], [Case] |
| Thermal | Element, [dT_axial], [dT_grad_y], [h_y], [dT_grad_z], [h_z], [Case] |
| Settlement | Node, Dof, Value |
| Prestress | Element, P, [e_i], [e_j], [plane], [sag], [Case] |

I nomi dei fogli e delle colonne sono riconosciuti senza distinzione di maiuscole.