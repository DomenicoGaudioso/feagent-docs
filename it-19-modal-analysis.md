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

> Cross-validazione: le frequenze coincidono con un solutore FEM esterno indipendente a
> precisione macchina (vedi `validation/validate_modal_ext.py`).

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
from feagent.plotting import plot_mode
plot_mode(mr, 0).show()     # 1° modo (indice 0); ampiezza auto-scalata
```

## Time-history lineare (Newmark e sovrapposizione modale)

Integrazione al passo dell'equazione del moto `M ü + C u̇ + K u = F(t)`
(Newmark 1959, accelerazione media; Chopra, *Dynamics of Structures*, 5ª ed.):

```python
from feagent import RayleighDamping
damp = RayleighDamping.from_frequencies(1.0, 0.05, 8.0, 0.05)

# forzanti nodali: funzioni f(t) o storie campionate a passo dt
res = m.solve_time_history({(5, "uy"): lambda t: 1e3*np.sin(20*t)},
                           dt=0.005, t_end=10.0,
                           mass_source={"G": 1.0}, damping=damp)

# accelerogramma alla base: carico efficace -M·ι·üg(t), spostamenti relativi
res = m.solve_time_history(("x", ug), dt=0.005, t_end=20.0,
                           mass_source={"G": 1.0}, damping=damp)

res.displacement_history(5, "uy")          # storie u(t), v(t), a(t)
res.max_displacement(5, "uy")              # estremi
res.reaction_history(1, "uy")              # reazioni
res.element_end_force_history(3)           # forze d'elemento (storia)
res.absolute_acceleration_history(5, "ux") # accelerazione assoluta (sisma)
```

La **variante modale** integra le equazioni disaccoppiate per modo con la
soluzione esatta a tratti (interpolazione lineare dell'eccitazione, Chopra
§5.2 — esatta e stabile per qualunque dt) oppure con Newmark per modo:

```python
res = m.solve_time_history_modal(exc, dt=0.005, t_end=10.0, n_modes=12,
                                 mass_source={"G": 1.0}, damping=0.05)
```

`damping` accetta un rapporto ξ scalare, un array per modo o un
`DampingModel` proiettato sui modi. Con smorzamento classico (Rayleigh) e
tutti i modi, la variante modale con `method="newmark"` coincide con
l'integrazione diretta alla precisione macchina.

### Matrice di massa consistente

L'analisi modale (e la time-history) accettano `mass="consistent"`: al posto
della massa concentrata diagonale (default `"lumped"`) si usa la matrice di
massa consistente (Przemieniecki 1968), che include l'inerzia rotazionale
delle travi e converge alle frequenze del continuo molto più rapidamente a
parità di mesh.

```python
mc = m.modal(n_modes=6, mass_source={"G": 1.0}, mass="consistent")
```

Inoltre la massa propria dei **gusci** (ρ·t + eventuale sovrappeso) entra
automaticamente in modale e dinamica, come per travi e bielle.

### Vincoli cinematici in P-Delta e dinamica

I vincoli cinematici (link rigidi, equalDOF, diaframmi, appoggi inclinati)
sono componibili anche con l'analisi del secondo ordine `solve_pdelta` e con
la time-history (diretta e modale): la trasformazione master–slave `u = W u_r`
riduce `K`, `M` e la rigidezza geometrica `K_g`, e gli spostamenti completi
si ricostruiscono con `u = W u_r`. Restano esclusi solo i carichi mobili
dinamici (`moving_load_dynamic_analysis`).

## Risposta armonica a regime

`solve_harmonic` risolve la risposta stazionaria `(K - Om^2 M + i Om C) U = F`
a una o piu' pulsazioni forzanti (funzione di trasferimento):

```python
Om = np.linspace(0.1, 3.0, 200) * omega0
hr = m.solve_harmonic({(5, "uy"): 1e3}, Om, mass_source={"G": 1.0},
                      damping=damp)
hr.amplitude(5, "uy")   # |U(Om)|   (ampiezza a regime)
hr.phase(5, "uy")       # arg U(Om) (ritardo di fase)
hr.peak(5, "uy")        # (Om, ampiezza) del picco di risonanza
```

La forzante puo' essere `{(nodo, gdl): ampiezza}` (ampiezza complessa per la
fase) o `(direction, a0)` per l'accelerazione armonica alla base. Riusa la
condensazione dei GdL senza massa, i vincoli cinematici e `mass="consistent"`.
