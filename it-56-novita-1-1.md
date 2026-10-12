---
layout: default
title: "56 - Novità della versione 1.1"
parent: Italiano
nav_order: 56
---

# 56 - Novità della versione 1.1

Questa pagina raccoglie ciò che è cambiato dalla 0.9 alla 1.1 e dice, per ogni
novità, come si usa e dove leggere i dettagli. La 1.1 è la prima versione con un
eseguibile dopo la 0.9: comprende anche il lavoro della 1.0 (il sistema dei
plugin).

## Come aggiornare

La release [v1.1.0](https://github.com/DomenicoGaudioso/feagent/releases/tag/v1.1.0)
contiene tre file:

| File | A cosa serve |
|---|---|
| `feagent-gui-windows.exe` | l'interfaccia desktop per Windows, senza Python; eseguibile protetto, licenza su un PC |
| `feagent-1.1.0-py3-none-any.whl` | il pacchetto Python, da installare con pip |
| `feagent-1.1.0.tar.gz` | i sorgenti della versione |

```bash
pip install "feagent[all] @ file:///C:/Download/feagent-1.1.0-py3-none-any.whl"
```

Con un clone basta `git pull` e `pip install -e ".[all]"`. Il calcolo parallelo
usa anche `threadpoolctl`, compreso negli extra `gui`, `parallel` e `all`.
Installazione completa in [01 - Installazione](it-01-installation.html).

## Ottimizzazione topologica

Toglie il materiale dove non serve, su mesh di gusci o di solidi, con i metodi
SIMP (densità continue) e BESO (elementi pieni o vuoti); dal risultato si ricava
il modello ridotto, che si rianalizza come qualunque altro.

```python
from feagent.topology import ottimizza

r = ottimizza(model, frazione=0.3, metodo="beso", casi={"Q": 1.0})
m2 = r.modello_ridotto()
```

Nell'interfaccia: **Analisi > Ottimizzazione topologica (SIMP/BESO)…**, poi
**Risultati > Storia dell'ottimizzazione** e **Risultati > Apri il modello
ridotto**.

Nella 1.1 BESO non stacca più il carico dai vincoli (prima, con un carico
concentrato e frazioni basse, la deformabilità poteva finire a 10⁸-10⁹), e il
modello ridotto SIMP di una lastra sottile si costruisce in pochi secondi senza
falsi meccanismi. Esempio guidato, scelta del metodo, parametri e messaggi in
[55 - Ottimizzazione topologica](it-55-ottimizzazione-topologica.html).

![Densità BESO](images/gui_topology_beso.png)
*Figura 1 — Lastra a mensola ottimizzata con BESO nell'interfaccia: gusci pieni (arancio) o vuoti (viola).*

## Tensioni in travi e gusci, sezioni composte per fasi

Le travi hanno ora le tensioni normali nelle fibre della sezione e i gusci le
tensioni sulle facce. Le sezioni composte si descrivono per parti (foglio
`SectionPart`: acciaio, soletta, armature) e per fasi (foglio `StressPhase`:
getto, permanenti, ritiro, traffico, ciascuna con il suo coefficiente *n* e i
suoi casi di carico), e le tensioni si sommano fase per fase.

Nell'interfaccia: **Risultati > σ max travi / σ min travi**, **Tensione in una
fibra**, la vista estrusa colorata (tasto E) e **Tensioni nella sezione…** con il
diagramma σ(y). Vedi [47 - Tensioni](it-47-tensioni.html).

## Calcolo parallelo e multithread

Le analisi indipendenti (casi, combinazioni, carichi mobili, analisi dinamiche)
si possono distribuire su più processi, e l'algebra lineare su più thread. È
spento di default; il numero di processi e di thread si ricava dal PC.

```python
import feagent.parallel as par

par.configure(parallel=True)                 # processi automatici
results = m.solve_many(combos)
```

Da riga di comando `--parallel`, `--workers N`, `--multithread`, `--threads N`;
nell'interfaccia **Strumenti > Opzioni > Calcolo**. Tempi e consigli in
[29 - Prestazioni](it-29-performance.html).

## Plugin

Strumenti costruiti sul motore di feagent, raggiungibili dal menu **Plugin**
dell'interfaccia, da `feagent plugin` e dai tool dell'IA:

| Plugin | Che cosa fa | Pagina |
|---|---|---|
| Bridge | modello a graticcio di impalcati misti e in c.a. | [47 - Plugin Bridge](it-plugin-bridge.html) |
| Train | transito di un convoglio su un ponte | [48 - Plugin Train](it-plugin-train.html) |
| Frames | telai piani, spaziali e con nucleo | [49 - Plugin Frames](it-plugin-frames.html) |
| TrussGirder | travi reticolari: statica, modale, pushover | [50 - Plugin TrussGirder](it-plugin-trussgirder.html) |
| DigitalTwin | gemello digitale: confronto con l'identificazione dinamica, sensibilità, calibrazione | [51 - Plugin DigitalTwin](it-plugin-digitaltwin.html) |
| MasoBridge | ponti ad arco in muratura a elementi solidi: statica, linea delle pressioni, portanza non lineare, sisma | [52 - Plugin MasoBridge](it-plugin-masobridge.html) |
| GerberNL | selle Gerber con le armature reali, analisi non lineare fino alla rottura | [46 - Plugin](it-46-plugin.html) |

Come si scrive un plugin: [46 - Plugin](it-46-plugin.html).

## Solidi non lineari

I modelli di soli esaedri hanno un solutore non lineare (Newton a carico o a
spostamento controllato) con i legami Drucker-Prager, muratura Rankine e cono e
danno trazione/compressione integrato con IMPL-EX, le barre d'armatura annegate
negli esaedri, le piastre rigide su perno e la time-history. Oggi si usa
attraverso i plugin MasoBridge e GerberNL: vedi
[43 - Elementi solidi](it-43-solid-elements.html) e
[52 - Plugin MasoBridge](it-plugin-masobridge.html).

## Eseguibile, licenza e registrazione dell'uso

L'eseguibile per Windows è protetto (il codice è compilato) e si attiva con un
account civilBox su un PC per account; la schermata di accesso ha il
collegamento per registrarsi su civilbox.xyz. L'eseguibile registra ogni avvio
e ogni analisi (programma, versione, sistema, mai i dati del modello), anche
senza account; il pacchetto installato con pip non invia nulla. Vedi
[42 - Interfaccia, Registrazione dell'uso](it-42-desktop-gui.html#registrazione-delluso).

## Altre correzioni della 1.1

* Mappa delle densità a colore uniforme per guscio: la media sui nodi faceva
  sembrare grigio anche il risultato di BESO.
* Il modello ridotto aperto dall'interfaccia conserva l'asse verticale del
  modello di partenza e non porta più i nodi rimasti senza gusci (prima
  comparivano come centinaia di vincoli).
* Ricerca dei meccanismi del modello ridotto sulla rigidezza scalata: prima,
  con le lastre sottili, poteva durare minuti e consegnare un modello singolare.
