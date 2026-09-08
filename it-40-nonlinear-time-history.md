---
layout: default
title: "40 - Time-history non lineare (isolatori, dissipatori)"
parent: Italiano
nav_order: 40
---

# 40 - Time-history non lineare (isolatori, dissipatori)

`feagent.nl_dynamics` integra l'equazione del moto di una **struttura lineare con
dispositivi non lineari**: isolatori a pendolo ed elastomerici, dissipatori isteretici
e viscosi, gap e ritegni. E' l'analisi che le norme chiedono per il progetto
dell'isolamento sismico (EN 1998-2 par. 7, NTC 2018 par. 7.10): impalcato e pile restano
elastici, i dispositivi concentrano la dissipazione e l'input e' un insieme di
accelerogrammi.

```python
from feagent import (Model, NonlinearLink, FrictionPendulum, Bilinear,
                     ViscousDamper, Gap, solve_time_history_nl)

links = [NonlinearLink(1, node_i=3, node_j=5, law=FrictionPendulum(W=2.9e6, R=3.1, mu=0.05),
                       axes=("x", "y"))]
res = solve_time_history_nl(model, links, [("x", ugx), ("y", ugy)], dt=0.005, t_end=20.0,
                            mass_source={"M": 1.0}, damping=damp)
d, F = res.hysteresis(1)                # ciclo del dispositivo 1 (direzione x)
res.link_max_displacement(1)            # spostamento di progetto dell'isolatore
res.energy_dissipated(1)                # energia isteretica [J]
res.base_shear_history("x")             # forza trasmessa dai dispositivi a terra
```

## Modello

Un `NonlinearLink` collega `node_i` e `node_j` (oppure `node_j=None` per un dispositivo
verso il suolo) e agisce sulle **traslazioni relative** `d = u_j - u_i` lungo gli assi
globali scelti (`("x", "y")` per un isolatore in pianta, `("x",)` per un dissipatore
longitudinale). La struttura lineare e' un qualsiasi modello feagent: travi, gusci,
molle lineari, vincoli cinematici (`add_equal_dof` e' il modo naturale per far seguire
all'impalcato la testa pila in verticale e in rotazione, lasciando al link lo
scorrimento orizzontale). I nodi di un link devono avere massa (di norma ce l'hanno:
li' sta la massa dell'impalcato).

## Leggi dei dispositivi

| Legge | Forza | Uso tipico |
|---|---|---|
| `Bilinear(k1, k2, Fy, coupled=True)` | elasto-plastico con incrudimento cinematico in parallelo a `k2`: rigidezza iniziale `k1`, post-snervamento `k2`, forza di snervamento `Fy` | isolatori elastomerici con nucleo in piombo, HDRB (bilineare equivalente), dissipatori isteretici |
| `FrictionPendulum(W, R, mu, mu_slow=None, a=50, u_y=5e-4)` | `F = (W/R) d + mu W sign(v)` con rigidezza di aderenza `mu W / u_y`; attrito dipendente dalla velocita' opzionale `mu(v) = mu_fast - (mu_fast - mu_slow) exp(-a|v|)` (Constantinou et al. 1990) | pendolo a scorrimento singolo; il periodo `2π√(R/g)` non dipende dalla massa |
| `ViscousDamper(c, alpha)` | `F = c |v|^alpha sign(v)` (regolarizzata vicino a `v = 0` per `alpha < 1`) | dissipatori fluido-viscosi (EN 15129 par. 7) |
| `Gap(k, gap, sign)` | rigidezza di contatto `k` oltre il gioco `gap` | ritegni sismici, martellamento sulle spalle |

Con `coupled=True` (default) le leggi bilineari usano una **superficie di snervamento
circolare** nel piano delle due direzioni del link con aggiornamento a ritorno radiale
(Park, Wen & Ang 1986): la forza sotto moto simultaneo in X e Y e' quella del
dispositivo reale, non di due molle monoassiali indipendenti.

## Integrazione e smorzamento

Newmark ad accelerazione media con iterazioni di Newton-Raphson sul residuo a ogni
passo (tangente consistente del ritorno radiale, `tol` e `max_iter` regolabili); il
risultato riporta le iterazioni per passo e il flag `converged`. Lo smorzamento viscoso
`C = a M + b K` e' costruito sulla **sola struttura lineare**: i dispositivi non stanno
in `K`, quindi non nasce smorzamento spurio proporzionale alla rigidezza sul moto
dell'isolatore (Ryan & Polanco 2008). In una struttura isolata i gradi di liberta'
orizzontali dell'impalcato non hanno rigidezza lineare e la modale del modello nudo non
e' possibile: lo smorzamento si calibra su un modello di servizio con i dispositivi
sostituiti dalla rigidezza efficace (`add_elastic_support`), preferibilmente
proporzionale alla rigidezza sulle sole pile (vedi
`examples/ex18_isolated_bridge_fps.py`).

## Moto del suolo e accelerogrammi

L'eccitazione e' la stessa della time-history lineare, piu' **piu' componenti del moto
del suolo simultanee**: `[("x", ugx), ("y", ugy)]`. `absolute_acceleration_history`
somma l'accelerazione del suolo di ogni componente. `feagent.accelerograms` fornisce gli
strumenti di contorno:

- `read_accelerogram`, `resample`, `baseline_correction`;
- `response_spectrum(ug, dt, periods, xi)` con la ricorrenza esatta di Nigam-Jennings
  (pseudo-accelerazione, spostamento, accelerazione assoluta);
- `spectrum_compatible_accelerogram(target, duration, dt, seed=...)` genera un
  accelerogramma artificiale compatibile con uno spettro target (Gasparini & Vanmarcke,
  SIMQKE), per esempio `seismic.ResponseSpectrum.eurocode8(ag, soil)`;
- `spectrum_match_check(records, dt, target, T1)` applica la regola EN 1998-1 par.
  3.2.3.1.2 / NTC 2018 par. 3.2.3.6 (spettro medio non inferiore al 90 % del target nel
  campo di periodi di interesse).

## Validazione

Il solutore e' confrontato con OpenSees eseguito in locale e con risultati in forma
chiusa; le tabelle complete sono in [39 - Report dei benchmark](it-39-benchmark-report.html):

- isolatore bilineare e pendolo a scorrimento sotto accelerogramma EC8-compatibile
  contro `zeroLength + Steel01`: storie di spostamento e forza entro lo 0,03 % del picco;
- isolatore bidirezionale accoppiato sotto due componenti simultanee contro
  `elastomericBearingPlasticity`: entro lo 0,003 %;
- dissipatore viscoso non lineare contro il materiale `Viscous`: entro lo 0,003 %;
- impalcato su due pendoli sotto due componenti: bilancio energetico
  `E_in = E_k + E_s + E_d + E_h` chiuso a 1e-12 %, forze dei dispositivi pari
  all'inerzia dell'impalcato;
- limite elastico: identico al solutore lineare alla precisione di macchina.

## Esempio: impalcato da ponte su pendoli a scorrimento

`examples/ex18_isolated_bridge_fps.py` analizza un impalcato a tre campate (3 x 40 m)
su due pile e due spalle con un pendolo a scorrimento per ogni appoggio (R = 3,1 m,
periodo 3,5 s, attrito 5 % veloce / 3 % lento), sotto sette coppie di accelerogrammi
spettro-compatibili (EC8, ag = 0,35 g, suolo B) con il controllo di compatibilita', e
riporta lo spostamento di progetto dei dispositivi, i cicli isteretici, la forza
trasmessa alle sottostrutture e l'energia dissipata.

![Impalcato isolato su pendoli a scorrimento](images/ex18_isolated_bridge_fps.png)
