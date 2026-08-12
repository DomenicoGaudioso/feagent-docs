---
layout: default
title: "19 - Analisi Modale"
parent: Italiano
nav_order: 19
---

# 19 - Analisi modale e combinazioni con coefficienti

## Combinazioni con coefficienti moltiplicativi

`Model.solve` accetta come `cases` un **dizionario `{case: coefficiente}`** per
combinare i load pattern con fattori (es. SLU NTC):

```python
res = m.solve(cases={"G": 1.35, "Q": 1.5})        # 1.35·G + 1.5·Q
res = m.solve(cases={"G": 1.0, "Q": 0.3, "N": 1.0})  # combinazione qualsiasi
```

Spostamenti, reazioni e **azioni interne** rispettano i coefficienti (linearità
verificata). `cases` resta utilizzabile anche come stringa o lista (coeff 1).

## Masse dai carichi

L'utente decide **quali load case trasformare in massa** e con quale coefficiente,
tramite un *mass source* `{case: coefficiente}`. La massa è ricavata dalle forze:
`massa = coeff · |forza| / g`, attribuita ai 3 GdL traslazionali del nodo (massa
concentrata). Sono trasformati in massa sia i **carichi distribuiti** (ripartiti
ai nodi tramite le forze nodali equivalenti) sia quelli **concentrati**
(nodali e in campata). Mettere nella sorgente i load case gravitazionali.

```python
M = m.assemble_mass({"G": 1.0, "Q": 0.3})   # vettore masse (diagonale)
```

## Analisi modale

```python
mr = m.modal(n_modes=6, mass_source={"G": 1.0, "Q": 0.3}, g=9.81)

for i in range(len(mr.freq)):
    print(mr.freq[i], "Hz", mr.period[i], "s")
mp = mr.mass_participation()     # rapporti di massa partecipante (n_modi x 3: X,Y,Z)
```

`modal()` risolve `K φ = ω² M φ` sui GdL liberi; i GdL liberi **senza massa**
(rotazionali e traslazionali scarichi) sono eliminati per **condensazione
statica**, evitando i modi spuri. Il risultato è un `ModalResult` con
`omega`, `freq` [Hz], `period` [s], `phi` (forme modali normalizzate a massa),
`eff_mass` e `mass_participation()` per direzione.

> Cross-validazione: le frequenze coincidono con **OpenSees** (`ops.eigen`) a
> precisione macchina (vedi `validation/validate_modal_opensees.py`).

### Esempio illustrato (telaio piano a 2 piani)

Masse dai carichi gravitazionali sui traversi; primi tre modi (vincolati i GdL
fuori-piano per un'analisi 2D):

| Carichi (sorgente di massa) | Modo 1 — sway | 
|---|---|
| ![](images/modal_loads.png) | ![](images/modal_mode1.png) |

| Modo 2 | Modo 3 |
|---|---|
| ![](images/modal_mode2.png) | ![](images/modal_mode3.png) |

Visualizzazione delle forme modali:

```python
from beamfeapy.plotting import plot_mode
plot_mode(mr, 0).show()     # 1° modo (indice 0); ampiezza auto-scalata
```
