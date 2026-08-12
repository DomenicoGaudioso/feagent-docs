---
layout: default
title: "16 - Galleria degli Esempi"
parent: "14 - Esempi d'Uso"
grand_parent: Italiano
nav_order: 1
---

# 16 - Galleria degli esempi (con grafici)

Questa pagina raccoglie **tutti i 24 esempi** di `usage_examples/` con i grafici
generati da beamfeapy. Le immagini sono prodotte eseguendo gli esempi reali
(`python scripts/_gen_example_images.py`, richiede `beamfeapy[plot]`); gli esempi
01, 10, 16, 17, 18, 20, 21 hanno solo output testuale e non producono grafici.

> Convenzione europea nei diagrammi del momento: **momento negativo
> all'estradosso** (positivo/inflessione all'intradosso).

---

### 01 — Mensola con carico in punta
Esempio più semplice: mensola incastrata con forza verticale all'estremo libero.
Verifica `δ = PL³/3EI` (errore ~1e-16). *(solo output testuale)*

### 02 — Trave appoggiata, carico distribuito uniforme
Trave appoggiata multi-elemento sotto carico uniforme. `M_max = qL²/8`,
`δ = 5qL⁴/384EI`.

| Deformata | Momento Mz |
|---|---|
| ![](images/ex02_deformed.png) | ![](images/ex02_Mz.png) |

### 03 — Carichi distribuiti parziali e trapezoidali
Trave con carico uniforme su tutta la luce + un tratto trapezoidale parziale.

| Carichi applicati | Momento Mz |
|---|---|
| ![](images/ex03_loads.png) | ![](images/ex03_Mz.png) |

### 04 — Carichi concentrati in campata
Mensola con forza concentrata e momento concentrato a punti interni (ξ∈[0,1]):
il diagramma mostra i salti in corrispondenza dei carichi.

| Deformata | Azioni interne |
|---|---|
| ![](images/ex04_deformed.png) | ![](images/ex04_internal_forces.png) |

### 05 — Carichi termici: uniforme e gradiente
Portale con variazione termica uniforme (+20°C) e gradiente sull'altezza.

| Deformata | Momento Mz |
|---|---|
| ![](images/ex05_deformed.png) | ![](images/ex05_Mz_thermal.png) |

### 06 — Profilo termico non lineare (EN 1991-1-5)
Profilo di temperatura non lineare sull'altezza: parte uniforme + lineare
(deformazioni) e residuo autoequilibrato (eigenstress).

![](images/ex06_deformed_thermal_profile.png)

### 07 — Cedimenti nodali
Trave appoggiata con cedimento verticale di 5 mm di un appoggio.

| Deformata | Momento Mz |
|---|---|
| ![](images/ex07_settlement_deformed.png) | ![](images/ex07_settlement_Mz.png) |

### 08 — Precompressione con cavo parabolico
Trave appoggiata, cavo parabolico (P=2 MN): momento primario `M = P·e`, camber.

| Deformata (camber) | Azioni interne |
|---|---|
| ![](images/ex08_prestress_deformed.png) | ![](images/ex08_prestress_forces.png) |

### 09 — Precompressione dalla geometria 3D del cavo
Trave continua a 2 campate; il cavo è definito dalle coordinate 3D (forze di
ancoraggio + deviazione). Compaiono i momenti secondari iperstatici.

| Deformata | Azioni interne |
|---|---|
| ![](images/ex09_cable_deformed.png) | ![](images/ex09_cable_forces.png) |

### 10 — Timoshenko vs Eulero-Bernoulli
Trave tozza (L/h piccolo): la freccia di Timoshenko è maggiore (taglio). *(solo
output testuale; vedi [Timoshenko e rilasci](it-06-timoshenko-releases.html))*

### 11 — Rilasci di estremità (cerniere)
Confronto trave continua vs trave con cerniera interna (svincolo del momento).

| Continua — deformata | Continua — Mz |
|---|---|
| ![](images/ex11a_continuous_deformed.png) | ![](images/ex11a_continuous_Mz.png) |

| Con cerniera — deformata | Con cerniera — Mz |
|---|---|
| ![](images/ex11b_hinge_deformed.png) | ![](images/ex11b_hinge_Mz.png) |

### 12 — Trave a sezione variabile (tapered)
Mensola rastremata con un **solo elemento** (rigidezza esatta force-based).

| Deformata | Azioni interne |
|---|---|
| ![](images/ex12_tapered_deformed.png) | ![](images/ex12_tapered_forces.png) |

### 13 — Tapered: sezioni per ID e stazioni
Tre modi di definire la sezione variabile: lambda, sezioni per ID start/end,
stazioni a varie ascisse.

| via lambda | via ID start/end | per stazioni |
|---|---|---|
| ![](images/ex13_tapered_lambda.png) | ![](images/ex13_tapered_sectid.png) | ![](images/ex13_tapered_stations.png) |

### 14 — Portale 3D con carichi multipli
Portale 3D (colonne + traverso) con carico distribuito e forze nodali.

| Deformata | Momento Mz | Reazioni |
|---|---|---|
| ![](images/ex14_portal_deformed.png) | ![](images/ex14_portal_Mz.png) | ![](images/ex14_portal_reactions.png) |

### 15 — Load case e combinazioni
Trave con casi G (permanente) e Q (variabile); soluzione della combinazione.

| Carichi G | Carichi Q | Mz combinazione |
|---|---|---|
| ![](images/ex15_loads_G.png) | ![](images/ex15_loads_Q.png) | ![](images/ex15_Mz_combined.png) |

### 16 — Orientazione della sezione (ref_vector, roll)
Effetto del vettore di riferimento e dell'angolo di roll sugli assi locali in 3D.
*(solo output testuale; vedi [Orientazione sezione](it-08-section-orientation.html))*

### 17 — Tipi di vincolo
`fix`, `pin`, `support` selettivo, cedimenti. *(solo output testuale)*

### 18 — Post-processing: azioni interne
Calcolo e accesso a N, Vy, Vz, T, My, Mz lungo gli elementi. *(solo output
testuale; vedi [Post-processing](it-09-post-processing.html))*

### 19 — Tutte le funzioni di visualizzazione
Dimostra ogni funzione di `beamfeapy.plotting`.

| Modello | Carichi G | Carichi Q |
|---|---|---|
| ![](images/ex19_model.png) | ![](images/ex19_loads_G.png) | ![](images/ex19_loads_Q.png) |

| Mz | Deformata | Reazioni |
|---|---|---|
| ![](images/ex19_diagram_Mz.png) | ![](images/ex19_deformed.png) | ![](images/ex19_reactions.png) |

I 6 diagrammi (N, Vy, Vz, T, My, Mz) sulla struttura:

| N | Vy | Vz |
|---|---|---|
| ![](images/ex19_diagram_N.png) | ![](images/ex19_diagram_Vy.png) | ![](images/ex19_diagram_Vz.png) |
| **T** | **My** | **Mz** |
| ![](images/ex19_diagram_T.png) | ![](images/ex19_diagram_My.png) | ![](images/ex19_diagram_Mz.png) |

### 20 — Workflow Excel I/O
Genera template, legge il modello, risolve, esporta i risultati. *(solo output
testuale; vedi [Excel I/O](it-11-excel-io.html))*

### 21 — Solver sparso per modelli grandi
Confronto tempi denso vs sparso al crescere dei GdL. *(solo output testuale;
vedi [Solver sparso](it-12-sparse-solver.html))*

### 22 — Trave continua multi-campata
Trave continua a 3 campate con vari pattern di carico: momenti negativi sugli
appoggi interni, positivi in campata.

| Deformata | Momento Mz |
|---|---|
| ![](images/ex22_continuous_deformed.png) | ![](images/ex22_continuous_Mz.png) |

### 23 — Telaio con cerniere interne
Portale con cerniere interne: ridistribuzione delle sollecitazioni.

| Deformata | Momento Mz | Reazioni |
|---|---|---|
| ![](images/ex23_hinge_frame_deformed.png) | ![](images/ex23_hinge_frame_Mz.png) | ![](images/ex23_hinge_frame_reactions.png) |

### 24 — Momenti secondari da precompressione (iperstatica)
Confronto isostatica (solo momenti primari) vs iperstatica (compaiono i secondari).

| Isostatica — deformata | Isostatica — Mz |
|---|---|
| ![](images/ex24a_det_deformed.png) | ![](images/ex24a_det_Mz.png) |

| Iperstatica — deformata | Iperstatica — Mz |
|---|---|
| ![](images/ex24b_ind_deformed.png) | ![](images/ex24b_ind_Mz.png) |

---

> Rigenerazione immagini: `python scripts/_gen_example_images.py` (gallery completa)
> e `python scripts/_gen_images.py` (illustrazioni delle pagine tematiche).

---

## Esempi avanzati (26–28)

Per i tre casi studio complessi (palazzina 9 piani, grigliato 3D, grattacielo 50
piani) con script completi e galleria dettagliata, vedi la pagina dedicata:
[**Casi Studio Avanzati**](it-26-advanced-case-studies.html).
