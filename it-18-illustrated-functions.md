---
layout: default
title: "18 - Funzioni Illustrate"
parent: Italiano
nav_order: 18
---

# 18 - Funzioni illustrate

Ogni famiglia di funzioni della libreria è qui spiegata e accompagnata da un
esempio completo, illustrato con i cinque quadri standard: **modello**, **carichi
applicati**, **deformata**, **sollecitazioni** (diagramma `Mz`, convenzione
europea) e **reazioni vincolari**. Per le firme complete vedi
[API Reference](it-17-api-reference.html). Le immagini si rigenerano con
`python scripts/_gen_function_docs.py`.

> I diagrammi delle sollecitazioni sono **aree riempite bicolore per segno**
> (positivo / negativo con trasparenza): flessione positiva celeste, negativa
> rosso chiaro. Ogni azione è sul proprio asse di utilizzo (N su quello di My).

---

## 1. Carichi nodali — `add_nodal_load`

Forza e/o coppia concentrate a un nodo (sistema globale). Esempio: mensola con
forza verticale e momento all'estremo libero.

```python
m = Model(); m.add_node(1, 0, 0, 0); m.add_node(2, 4, 0, 0)
m.add_beam(1, 1, 2, Material(210e9, 0.3), Section(1e-2, 2e-5, 3e-5, 1e-5))
m.fix(1)
m.add_nodal_load(2, Fy=-10000.0, Mz=8000.0)
res = m.solve()
```

| Modello | Carichi | Deformata |
|---|---|---|
| ![](images/fn_nodal_model.png) | ![](images/fn_nodal_loads.png) | ![](images/fn_nodal_deformed.png) |

| Sollecitazioni (Mz) | Reazioni |
|---|---|
| ![](images/fn_nodal_forces.png) | ![](images/fn_nodal_reactions.png) |

---

## 2. Carichi concentrati in campata — `add_concentrated_load`

Forza/coppia a un'ascissa interna `xi ∈ [0,1]`. Il diagramma mostra il **salto**
in corrispondenza del carico.

```python
m.add_concentrated_load(1, 0.35, Fy=-2e4)     # forza a 0.35 L
m.add_concentrated_load(1, 0.70, Mz=1.5e4)    # momento a 0.70 L
```

| Modello | Carichi | Deformata |
|---|---|---|
| ![](images/fn_concentrated_model.png) | ![](images/fn_concentrated_loads.png) | ![](images/fn_concentrated_deformed.png) |

| Sollecitazioni (Mz) | Reazioni |
|---|---|
| ![](images/fn_concentrated_forces.png) | ![](images/fn_concentrated_reactions.png) |

---

## 3. Carichi distribuiti — `add_distributed_load`

Uniformi, parziali (`a,b`), trapezoidali (`q_i→q_j`); forze e momenti
(`fx,fy,fz,mx,my,mz`), in coordinate locali o globali.

```python
m.add_distributed_load(e, "fy", -3000.0)                 # uniforme
m.add_distributed_load(e, "fy", -4000.0)                  # tratto piu' caricato
```

| Modello | Carichi | Deformata |
|---|---|---|
| ![](images/fn_distributed_model.png) | ![](images/fn_distributed_loads.png) | ![](images/fn_distributed_deformed.png) |

| Sollecitazioni (Mz) | Reazioni |
|---|---|
| ![](images/fn_distributed_forces.png) | ![](images/fn_distributed_reactions.png) |

---

## 4. Carichi termici — `add_thermal_load` / `add_thermal_profile`

Variazione uniforme + gradiente lineare (o profilo generico). Esempio: portale
con `dT_axial` e gradiente sul traverso.

```python
m.add_thermal_load(2, dT_axial=25.0, dT_grad_y=20.0)
```

| Modello | Carichi (struttura+vincoli) | Deformata |
|---|---|---|
| ![](images/fn_thermal_model.png) | ![](images/fn_thermal_loads.png) | ![](images/fn_thermal_deformed.png) |

| Sollecitazioni (Mz) | Reazioni |
|---|---|
| ![](images/fn_thermal_forces.png) | ![](images/fn_thermal_reactions.png) |

> I carichi termici non sono frecce: il quadro "carichi" mostra struttura e
> vincoli; l'effetto si legge in deformata e sollecitazioni.

---

## 5. Cedimenti nodali — `add_settlement`

Spostamento/rotazione imposto a un vincolo. Esempio: appoggio che cede di 10 mm.

```python
m.add_settlement(node_dx, "uy", -0.01)
```

| Modello | Carichi | Deformata |
|---|---|---|
| ![](images/fn_settlement_model.png) | ![](images/fn_settlement_loads.png) | ![](images/fn_settlement_deformed.png) |

| Sollecitazioni (Mz) | Reazioni |
|---|---|
| ![](images/fn_settlement_forces.png) | ![](images/fn_settlement_reactions.png) |

---

## 6. Precompressione — `add_prestress` / `add_cable_prestress`

Cavo per eccentricità o da geometria 3D. Esempio: cavo parabolico (P=2 MN,
sag=0.35 m): momento primario `P·sag`, camber.

```python
m.add_prestress(1, P=2.0e6, sag=0.35)
```

| Modello | Carichi equivalenti | Deformata (camber) |
|---|---|---|
| ![](images/fn_prestress_model.png) | ![](images/fn_prestress_loads.png) | ![](images/fn_prestress_deformed.png) |

| Sollecitazioni (Mz) | Reazioni |
|---|---|
| ![](images/fn_prestress_forces.png) | ![](images/fn_prestress_reactions.png) |

---

## 7. Rilasci di estremità (cerniere) — `releases_i` / `releases_j`

Svincolo di un GdL all'estremo (condensazione statica). Esempio: trave con
cerniera flessionale interna sotto carico distribuito.

```python
m.add_beam(1, 1, 2, mat, sec, releases_j=["rz"])   # cerniera (Mz=0) all'estremo j
```

| Modello | Carichi | Deformata |
|---|---|---|
| ![](images/fn_releases_model.png) | ![](images/fn_releases_loads.png) | ![](images/fn_releases_deformed.png) |

| Sollecitazioni (Mz) | Reazioni |
|---|---|
| ![](images/fn_releases_forces.png) | ![](images/fn_releases_reactions.png) |

---

## 8. Sezione variabile (tapered) — `add_tapered_beam` / `VariableSection`

Membratura non prismatica con un solo elemento (rigidezza esatta). Esempio:
mensola rastremata 0.70 → 0.30 m con carico distribuito e in punta.

```python
vs = VariableSection.rectangular(0.30, lambda xi: 0.70 - 0.40 * xi)
m.add_tapered_beam(1, 1, 2, mat, vs)
```

| Modello | Carichi | Deformata |
|---|---|---|
| ![](images/fn_tapered_model.png) | ![](images/fn_tapered_loads.png) | ![](images/fn_tapered_deformed.png) |

| Sollecitazioni (Mz) | Reazioni |
|---|---|
| ![](images/fn_tapered_forces.png) | ![](images/fn_tapered_reactions.png) |

---

## 9. Timoshenko — `add_beam(..., shear=True)`

Trave tozza con deformabilità a taglio (aree `Asy`/`Asz`).

```python
sec = Section(A=A, Iy=..., Iz=..., J=..., Asy=5/6*A, Asz=5/6*A)
m.add_beam(1, 1, 2, mat, sec, shear=True)
```

| Modello | Carichi | Deformata |
|---|---|---|
| ![](images/fn_timoshenko_model.png) | ![](images/fn_timoshenko_loads.png) | ![](images/fn_timoshenko_deformed.png) |

| Sollecitazioni (Mz) | Reazioni |
|---|---|
| ![](images/fn_timoshenko_forces.png) | ![](images/fn_timoshenko_reactions.png) |

---

## 10. Telaio 3D — orientazione e analisi generale

Telaio 3D con elementi orientati lungo assi diversi (`ref_vector`), carichi
distribuiti e nodali. Mostra l'analisi 3D completa.

```python
m.add_beam(1, 1, 2, mat, sec, ref_vector=(1, 0, 0))   # colonna verticale
m.add_beam(2, 2, 3, mat, sec, ref_vector=(0, 0, 1))   # traverso
```

| Modello | Carichi | Deformata |
|---|---|---|
| ![](images/fn_frame3d_model.png) | ![](images/fn_frame3d_loads.png) | ![](images/fn_frame3d_deformed.png) |

| Sollecitazioni (Mz) | Reazioni |
|---|---|
| ![](images/fn_frame3d_forces.png) | ![](images/fn_frame3d_reactions.png) |
