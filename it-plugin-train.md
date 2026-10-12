---
layout: default
title: "Plugin Train: transito di un treno sul ponte"
parent: Italiano
nav_order: 48
---

# Plugin Train: transito di un treno sul ponte

Il plugin `train` simula il passaggio di un convoglio ferroviario su una trave
semplicemente appoggiata, per una scansione di velocità, e restituisce in mezzeria
accelerazione, velocità e spostamento verticali, il picco di accelerazione, la velocità
critica e il coefficiente di amplificazione dinamica (DAF). Riprende lo strumento
OPSTrainLoad (forza viaggiante 2D, interazione veicolo struttura 2D, impalcato 3D a
6 gradi di libertà con binario eccentrico) ma **il calcolo è tutto di feagent**: niente
OpenSees, niente dipendenza dai repository d'origine.

## Come si usa

```python
from feagent import plugins
p = plugins.get("train")
r = p.run("scansione_velocita", v_min=100, v_max=300, passo_v=20, modello_veicolo="tutti")
print(r.message)            # picco di accelerazione, DAF, velocità
r.tables, r.charts          # tabelle e curve velocità-accelerazione e velocità-DAF
```

Dalla CLI: `feagent plugin run train scansione_velocita --param v_min=100 --param v_max=300`.

## Azioni

| Azione | Cosa fa |
|---|---|
| `scansione_velocita` | transito a ogni velocità dell'intervallo; tabelle e curve di accelerazione, spostamento, DAF |
| `transito` | un solo transito, storie temporali di spostamento, velocità, accelerazione (e rotazione nel 3D) |
| `stima_analitica` | frequenza propria, velocità critica `2 L f1`, velocità di risonanza `f1 d / i` |
| `genera_ponte` | modello del ponte con il convoglio in una posizione (caso di carico `TRENO`) |
| `relazione_word` | relazione Word in stile aziendale della scansione (file a nome fisso, sovrascritto) |

Parametri principali: luce, passo di discretizzazione, area, inerzia, E, nu, massa per
unità di lunghezza, smorzamento; schema `2d` o `3d` (inerzia laterale, J, eccentricità
del binario, inerzia rotazionale); convoglio (`OPS_default` di 7 assi da 170 kN, `LM71`,
`uniforme`, `personalizzato` con tabella interasse/carico, o un veicolo del catalogo di
feagent); intervallo di velocità; modello di veicolo (`forza_viaggiante`,
`massa_viaggiante`, `oscillatore`, `tutti`); rigidezza e smorzamento della sospensione per
asse; passo di integrazione e tempo di vibrazione libera.

## Metodo

La trave è un `feagent.Model` di elementi di Eulero e Bernoulli (nel piano, oppure a 6 GdL
per nodo nel 3D). Le matrici `K` e `M` sono quelle assemblate da feagent, lo smorzamento è
di Rayleigh (`RayleighDamping`) tarato sul primo e sul terzo modo di flessione verticale, e
l'integrazione è lo schema di Newmark ad accelerazione media di `feagent.dynamics`.

Ogni asse alla posizione `x(t) = v t - d` ha un *vettore di contatto* `c`, costruito con le
funzioni di forma dell'elemento sottostante (e il braccio `e` per la torsione): lo
spostamento verso il basso del punto di contatto è `c u`, il carico `P` vale `P c` in forze
nodali. Con questo:

* **forza viaggiante**: solo `P c`; le matrici non cambiano;
* **massa viaggiante**: `M(t) = M + sum m c c^T`, con `m = P / g`;
* **oscillatore**: massa, molla e smorzatore per asse, integrati insieme al ponte. I gradi
  di libertà dei veicoli si eliminano sul passo di Newmark, lasciando al ponte la rigidezza
  efficace `K_eff + sum D c c^T` con `D = kk m a0 / (m a0 + kk)`, `kk = k + c_s a1`.
  Per `k` che tende a infinito si ritrova la massa viaggiante, per `m` che tende a zero la
  forza viaggiante.

Il DAF è il rapporto fra lo spostamento massimo dinamico e il massimo statico dello stesso
convoglio sulla stessa trave (linea di influenza del modello). La tabella dà anche il
rapporto con la prima velocità analizzata, che è la definizione dello strumento d'origine.

## Che cosa è stato portato e che cosa no

Portato: i tre modelli di veicolo, lo schema 2D e 3D, la scansione di velocità, la relazione
Word (riscritta; il repository d'origine non ha formule OMML, qui le formule sono in corsivo
indentato come da stile). Non portato: il binario su ballast con molle non lineari (le
rotaie, le molle verticali e longitudinali), le pagine Streamlit, il server dell'IA,
l'eseguibile e la licenza, perché fuori dal perimetro di un plugin del solutore.

## Differenze rispetto allo strumento d'origine

* **Segno della sospensione.** Nel codice d'origine la forza della sospensione sul ponte ha
  segno opposto a quello fisico (il ponte vede una rigidezza negativa). Il plugin usa il
  segno fisico. Con il segno corretto il codice d'origine e il plugin concordano entro lo
  0,4 % sugli spostamenti; con il segno d'origine lo scarto arriva al 2 % a 300 km/h.
* **Interpolazione.** L'originale ripartisce i carichi dei modelli VBI sui nodi con
  interpolazione lineare e integra il veicolo con Eulero; qui si usano le funzioni di forma
  di Hermite e Newmark accoppiato.
* **Velocità effettiva.** L'originale avanza gli assi di `Ltot / (n - 1)` per passo, con
  `n = int(Ltot / (v dt))`, quindi un po' più veloce di `v` (oltre l'1 %), e usa la
  posizione del passo precedente. Il plugin usa `v t`. Gli argomenti `speed_eff_ratio` e
  `lag_steps` di `engine.run_transit` riproducono l'originale.
* **Smorzamento di Rayleigh.** L'originale usa il primo e il terzo autovalore dell'intero
  problema, che per la trave appoggiata è il primo modo assiale (35 Hz), non il terzo
  flessionale (62,8 Hz). Il default `modi_rayleigh="flessionali"` prende i modi di flessione
  verticale; `"globali"` riproduce l'originale.
* **Intervallo.** `v_max` è incluso.

## Verifica

* carico singolo lento: `P L^3 / (48 E I)` (statico esatto a 1e-5, transito a 1 km/h entro 0,2 %);
* carico viaggiante non smorzato contro la serie modale: scarto sotto l'1 % a 36 km/h e a
  0,7, 0,9, 1,0, 1,1, 1,4 volte la velocità critica `2 L f1`; la curva del DAF segue la
  serie punto per punto (massimo circa 1,7 fra 0,6 e 0,75 `v_cr`, circa 1,57 alla risonanza);
* contro `moving_load_dynamic_analysis` di feagent: scarto 1e-9;
* contro il codice d'origine rieseguito (Python 3.10, OpenSees, solo una tantum): storie
  temporali della forza viaggiante a 1e-9, picchi a 1e-5, rotazione torsionale 3D entro
  0,1 %;
* contro i risultati salvati nel repository d'origine: spostamenti entro 1,5 % su 10
  velocità (scarto massimo 1,2 %, dovuto alle particolarità descritte sopra), statico
  3,10779 mm e `f1` = 6,97559 Hz identici;
* limiti: massa nulla = forza viaggiante a 1e-9, sospensione rigida = massa viaggiante
  (1 % sul picco), schema 3D simmetrico = 2D a 1e-8.

## Limiti noti

* Il veicolo ha il solo spostamento verticale per asse; non si tengono i termini di
  Coriolis e centripeto della massa che corre.
* Le accelerazioni dei modelli con massa dipendono dal passo `dt`; il plugin avvisa quando
  `dt` non risolve la frequenza di accoppiamento fra massa e ponte.
* Massa viaggiante nello schema 3D con binario eccentrico: la torsione è accoppiata alla
  massa e, se l'impalcato non ha inerzia rotazionale (`massa_rotazionale = 0`) o `dt` è
  grande (sopra circa 0,0025 s), la soluzione può diventare numericamente instabile. Si
  assegni l'inerzia rotazionale (circa `m (Iy + Iz) / A`) e si riduca `dt`. La forza
  viaggiante e l'oscillatore non soffrono di questo.
* Con una rigidezza di sospensione estrema (oltre 1e12 N/m) il condizionamento numerico
  peggiora.
