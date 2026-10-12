---
layout: default
title: "52 - Plugin MasoBridge"
parent: Italiano
nav_order: 52
---

# 52 - Plugin MasoBridge

Il plugin `masobridge` genera e analizza ponti ad arco in muratura con elementi solidi:
- anello, riempimento, timpani, parapetti, spalle e pile intermedie, a una o più campate;
- statica lineare, sezioni di controllo con la linea delle pressioni;
- portanza non lineare fino al collasso;
- time-history sismica non lineare in una direzione.

Il calcolo è **solo di feagent**, tutto in Python.

## Modello

La maglia è di esaedri a 8 nodi, conforme, organizzata per colonne verticali: a ogni ascissa i livelli
seguono intradosso, estradosso e quota stradale. Arco, riempimento, spalle e pile condividono i nodi.

| Gruppo | Materiale | Note |
|---|---|---|
| `arco` | muratura | `n_spessore` elementi nello spessore |
| `timpano`, `parapetto` | muratura | sui fili esterni della larghezza |
| `riempimento` | riempimento | `n_riempimento` strati fra estradosso e strada |
| `spalla`, `pila` | muratura | base incastrata a `profondita_spalla` sotto l'imposta |

Preset di letteratura (prove TRRL): `prestwood`, `bolton`, `torksey`, `preston`. Fissano luce, freccia,
spessore e riempimento; larghezza e materiali restano da indicare.

## Legami non lineari

| `modello_muratura` | Muratura | Riempimento | Note |
|---|---|---|---|
| `danno` (default) | danno trazione/compressione, fessure rotanti che si richiudono | Drucker-Prager | entrambi integrati con IMPL-EX: converge passo per passo; se un passo non è accurato si ferma e lo dice |
| `drucker` | Drucker-Prager quasi senza trazione | Drucker-Prager | ritorno implicito: con resistenza a trazione di pochi kPa il Newton può non convergere |
| `rankine_cono` | Rankine + cono con attrito | Drucker-Prager | non riproduce ancora l'anello nudo: solo per studio |

**Legame a danno** (`feagent.solid_nl_danno.DannoTraCo`). La tensione effettiva elastica si divide in
parte positiva e parte negativa con la decomposizione spettrale, e ciascuna ha il suo danno:

*σ = (1 − d⁺) σ̄⁺ + (1 − d⁻) σ̄⁻*

Faria, Oliver e Cervera, IJSS 1998.

- **Trazione:** Rankine con soglia *f_t*.
- **Compressione:** cono con biassiale 1,16 *f_c*.
- **Softening:** esponenziale, regolarizzato con l'energia di frattura e la dimensione d'elemento di
  ciascun gruppo, così l'energia dissipata non dipende dalla maglia.
- **Fessure che si richiudono:** in compressione conta solo *d⁻*, quindi la cerniera dell'arco torna a
  lavorare quando la sezione si richiude.

**IMPL-EX** (Oliver, Huespe e Cante, CMAME 2008):
- dentro il passo il danno e il flusso plastico del riempimento sono estrapolati dai passi precedenti, quindi
  il legame è regolare e il Newton converge in poche iterazioni;
- a fine passo lo stato si aggiorna in forma implicita;
- il passo si dimezza se le tensioni implicite non sono in equilibrio entro il 5 % dei carichi, e ricresce
  quando l'errore è piccolo;
- se un passo non converge neppure dimezzato, lo si ripete con la scissione delle tensioni bloccata alle
  direzioni d'inizio passo (lineare, quindi convergente). `SistemaNL.passi_riserva` conta questi passi.

## Azioni

| Azione | Cosa fa |
|---|---|
| `genera` | genera la maglia con carichi e vincoli |
| `statica` | statica lineare con un'impronta di carico; tensioni nell'anello |
| `sezioni` | N, V, M per sezione e linea delle pressioni, col terzo medio |
| `confronto_trave` | anello a solidi contro trave curva (benchmark lineare) |
| `immagini` | PNG di maglia, tensioni e linea delle pressioni |
| `portanza` | pesi a carico imposto, poi impronta a spostamento controllato fino al collasso; confronto con l'analisi limite dell'anello |
| `sisma` | accelerogramma spettro-compatibile NTC/EC8 alla base, una direzione, istanti di danneggiamento per gruppo |
| `carico_mobile` | inviluppo di un'impronta che scorre sulla luce (lineare) |

La portanza si ferma al **collasso** solo se il carico non cresce più su uno spostamento doppio, con passi
convergenti. Una mancata convergenza resta tale, e l'esito lo dice.

## Validazione

**Anello nudo** (geometria Prestwood, larghezza 1 m, striscia da 0,75 m a L/4, *f_t* = 10 kPa). Il limite
statico di Heyman (linea delle pressioni nello spessore, programma lineare in `limite.py`) vale **15,83 kN**.

| Legame | Maglia | Carico finale | Scarto | Tempo |
|---|---|---|---|---|
| danno | 24 × 3 | 18,7 kN | +18 % | 3 min |
| danno | 48 × 4 | 17,3 kN | +9,5 % | 8 min |
| danno | 24 × 4 | 18,6 kN | +17 % | 2 min |
| Drucker-Prager | 12 × 3 | 19,8 kN, poi non converge a 2,9 mm | +25 % | 29 min |
| Rankine + cono | 12 × 3 | diverge a 1,1 mm | | |

Con il danno la curva converge infittendo la maglia. L'eccesso residuo è il bloccaggio flessionale
dell'esaedro pieno; i modi incompatibili con il danno non convergono, quindi conviene almeno 4 elementi
nello spessore.

**Legame a punto e barra** (`tests/test_solid_nl_danno.py`):
- energia dissipata in trazione pari a *G_f/h* entro il 4 %;
- picchi a *f_t* e *f_c*;
- tangente esatta;
- richiusura delle fessure;
- barra che localizza la fessura nell'elemento debole con forza di picco *f_t A* ed energia *G_f A*.

## Ponte di Prestwood (prova TRRL)

Prova al vero di Page (1987) con crollo a **228 kN**, e modello 2D di riferimento di Simoncello et al. (2020)
a 245 kN:
- luce 6,55 m, freccia 1,428 m, arco spesso 0,22 m, larghezza 3,80 m;
- striscia di carico da 0,30 m a L/4;
- muratura con *f_c* 4,5 MPa ed *E* 4130 MPa; *f_t* = 1 kPa, il valore di letteratura;
- riempimento Mohr-Coulomb con φ 30° e *c* 5 kPa.

Non pubblicati, quindi ipotizzati: modulo del riempimento 200 MPa, dilatanze nulle, base delle spalle
incastrata 1,5 m sotto l'imposta.

Dati in `validation/masobridge/prestwood.py`, con `modello_muratura="danno"` e `ft_muratura=1000`.

| Maglia | Passi | Picco | Arresto | Passi di riserva | Tempo |
|---|---|---|---|---|---|
| 16 × 3 | 1000 | 149,2 kN a 1,47 mm | passo non accurato a 1,49 mm | 39 | 20 min |
| 24 × 4 | 500 | 134,8 kN a 1,41 mm | passo non accurato al picco | 9 | 13 min |

Il calcolo arriva al picco, con fase dei pesi e ramo di carico regolari, e lì si ferma: lo stato implicito
non regge più il carico. È coerente con il crollo improvviso a quattro cerniere della prova, ma per seguire
il ramo dopo il picco serve un controllo ad arco (non ancora disponibile).

Il picco è **il 35-40 % sotto la prova**. Il modello non è tarato:
- il modulo e la resistenza del riempimento, l'attrito fra riempimento e timpani e la rigidezza delle spalle
  non sono noti;
- il taglio verticale all'imposta (vedi i limiti) cambia la cerniera d'imposta;
- il bloccaggio dell'esaedro pieno lavora nel verso opposto, rendendo il modello più resistente.

Lo studio di taratura è il passo successivo.

Con i legami plastici (`drucker`) lo stesso ponte non passava la fase dei pesi con *f_t* = 1 kPa. Con
*f_t* = 10 kPa si fermava per mancata convergenza tra 0,4 e 2,6 mm: vedi `validation/masobridge/README.md`.

## Limiti noti

- L'arco termina con un taglio verticale all'imposta, non radiale: a Prestwood la sezione d'imposta è alta
  0,317 m invece di 0,22 m. Il confronto con Heyman usa la stessa geometria ed è coerente; con i ponti
  reali sovrastima la cerniera all'imposta.
- Nessuna interfaccia fra arco e riempimento né fra gli anelli: la maglia è conforme.
- Archi con freccia oltre 0,35 volte la luce: gli esaedri in colonna si distorcono vicino alle imposte; la
  maglia radiale non è ancora disponibile.
- IMPL-EX ha un errore del primo ordine nel passo: i risultati vanno confermati con un numero di passi doppio.
