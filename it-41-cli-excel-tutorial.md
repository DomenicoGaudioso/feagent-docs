---
layout: default
title: "41 - Tutorial CLI: modelli Excel passo passo"
parent: Italiano
nav_order: 41
---

# 41 - Tutorial CLI: da un workbook Excel ai risultati, passo passo

Questo tutorial porta da un'installazione nuova a un modello risolto usando
**solo il terminale ed Excel**: niente codice Python. Usa gli esempi pronti
distribuiti con feagent, poi mostra come scrivere un proprio workbook foglio
per foglio, validarlo, risolvere una o piu' combinazioni di carico, leggere i
risultati e produrre figure e relazione.

I comandi sono mostrati per PowerShell su Windows; sono identici su macOS e
Linux (bash/zsh). Il riferimento di ogni opzione e' in
[37 - Interfaccia a riga di comando](it-37-cli.html); il formato Excel e'
descritto in [11 - Excel I/O](it-11-excel-io.html).

## 1. Installare e controllare l'ambiente

Installa feagent con gli extra usati dalla CLI (Excel, grafici, relazione):

```bash
python -m venv .venv                      # facoltativo ma consigliato
.venv\Scripts\activate                    # Windows  (source .venv/bin/activate su macOS/Linux)
pip install "feagent[all]"                # oppure pip install -e ".[all]" da un clone
```

Poi lascia che feagent controlli se stesso:

```bash
feagent doctor
```

<div align="center">
  <img src="images/cli_doctor.png" alt="feagent doctor" width="640">
</div>

`doctor` dice quali pacchetti opzionali mancano e la riga `pip` esatta per
installarli, se il comando `feagent` e' sul PATH (se non lo e', usa
`python -m feagent ...` oppure aggiungi al PATH la cartella `Scripts`
indicata) ed esegue un auto-test del solutore. Le note complete di
installazione, con la risoluzione dei problemi, sono in
[01 - Installazione](it-01-installation.html).

## 2. Partire da un esempio funzionante

Il modo piu' rapido per vedere com'e' organizzato un workbook e' scrivere uno
degli esempi distribuiti e aprirlo in Excel:

```bash
feagent examples                          # il catalogo
feagent examples simply_supported --verify
```

<div align="center">
  <img src="images/cli_examples_verify.png" alt="feagent examples simply_supported --verify" width="640">
</div>

Il comando scrive `simply_supported.xlsx` nella cartella corrente, lo
rilegge, lo risolve e confronta i risultati con le formule chiuse della
trave appoggiata (`5 q L^4 / 384 E I`, `P L^3 / 48 E I`, `q L / 2`,
`q L^2 / 8`, `P L / 4`): ogni verifica e' riprodotta a precisione macchina.
I dieci esempi coprono tutti i fogli del formato (carichi nodali,
distribuiti, concentrati, termici e di precompressione, cedimenti, cerniere,
elementi Timoshenko, telai 3D e graticci): scrivili tutti con
`feagent examples --all -o esempi/` e usali come modelli di partenza.

## 3. Anatomia del workbook

Crea il template e aprilo:

```bash
feagent template modello.xlsx
```

Il workbook e' una piccola mensola (due elementi) con un carico di ogni tipo,
tre casi di carico (`G`, `Q`, `T`) e tre combinazioni. Il primo foglio,
`README`, documenta ogni colonna, unita' e convenzione in inglese e in
italiano; viene ignorato in lettura, quindi puoi tenerlo anche nei tuoi file.

<div align="center">
  <img src="images/excel_readme.png" alt="foglio README" width="720">
</div>

Solo `Node` e' obbligatorio; ogni altro foglio puo' mancare o essere vuoto.
I nomi di fogli e colonne sono riconosciuti senza distinzione di maiuscole.
Le unita' sono SI (N, m, Pa, kg, K) o qualsiasi sistema coerente.

### Node

<div align="center">
  <img src="images/excel_node.png" alt="foglio Node" width="360">
</div>

| Colonna | Significato |
|---|---|
| `Node` | id intero, univoco |
| `X`, `Y`, `Z` | coordinate globali [m] |

Gli assi globali sono destrorsi. Per le travi orizzontali la verticale di
default e' **Y**: l'asse locale `y` dell'elemento coincide con Y globale,
quindi i carichi gravitazionali sono componenti `fy` con segno negativo. Usa
il piano X-Z per graticci e piastre viste in pianta (vedi l'esempio
`grillage`).

### Material

<div align="center">
  <img src="images/excel_material.png" alt="foglio Material" width="480">
</div>

| Colonna | Significato |
|---|---|
| `Material` | nome, richiamato in `Element` |
| `E` | modulo elastico [Pa] |
| `nu` | coefficiente di Poisson (default 0.3); `G` modulo di taglio opzionale |
| `alpha` | coefficiente di dilatazione termica [1/K], necessario ai carichi `Thermal` |
| `rho` | densita' di massa [kg/m^3], usata per le masse modali |

### Section

<div align="center">
  <img src="images/excel_section.png" alt="foglio Section" width="560">
</div>

| Colonna | Significato |
|---|---|
| `Section` | nome, richiamato in `Element` |
| `A` | area [m^2] |
| `Iy` | momento d'inerzia attorno all'asse locale `y` (flessione nel piano locale x-z, asse debole per la gravita') [m^4] |
| `Iz` | momento d'inerzia attorno all'asse locale `z` (flessione nel piano locale x-y, **asse forte per la gravita'**) [m^4] |
| `J` | costante di torsione [m^4] |
| `Asy`, `Asz` | aree di taglio, solo per gli elementi Timoshenko (`shear = 1`) |

### Element

<div align="center">
  <img src="images/excel_element.png" alt="foglio Element" width="720">
</div>

| Colonna | Significato |
|---|---|
| `Element` | id intero, univoco |
| `NodeI`, `NodeJ` | nodi di estremita'; l'asse locale `x` va da I a J |
| `Material`, `Section` | nomi dei fogli precedenti |
| `shear` | `1` = trave di Timoshenko (servono `Asy`, `Asz`), `0` o vuoto = Eulero-Bernoulli |
| `RefX`, `RefY`, `RefZ` | vettore di riferimento che definisce il piano locale x-y; vuoto = Y globale (X globale per gli elementi verticali) |
| `ReleasesI`, `ReleasesJ` | rilasci di estremita', separati da virgola tra `ux, uy, uz, rx, ry, rz` (`rz` = cerniera flessionale) |

Gli assi locali seguono la convenzione di SAP2000: `x` lungo l'elemento, `y`
dal vettore di riferimento, `z = x vettor y`. Per le **colonne verticali** il
riferimento di default e' X globale, quindi `Iz` governa lo spostamento nel
piano X; indica esplicitamente `RefX = 1, RefY = 0, RefZ = 0` (come fa
l'esempio `portal_frame`) quando vuoi esserne certo. Dettagli in
[08 - Orientazione della sezione](it-08-section-orientation.html).

### Support

<div align="center">
  <img src="images/excel_support.png" alt="foglio Support" width="420">
</div>

`Node` piu' sei flag 0/1 `Dx, Dy, Dz, Rx, Ry, Rz` negli assi **globali**
(`1` = vincolato). Un incastro ha sei uno; una cerniera `1 1 1 0 0 0`. Per
una trave piana nel piano X-Y vincola anche i gradi di liberta' fuori piano
(`Dz`, `Rx` alle estremita'), esattamente come fanno gli esempi.

### NodalLoad, DistributedLoad, ConcentratedLoad

<div align="center">
  <img src="images/excel_nodalload.png" alt="foglio NodalLoad" width="520">
</div>

<div align="center">
  <img src="images/excel_distributedload.png" alt="foglio DistributedLoad" width="560">
</div>

<div align="center">
  <img src="images/excel_concentratedload.png" alt="foglio ConcentratedLoad" width="600">
</div>

| Foglio | Colonne | Note |
|---|---|---|
| `NodalLoad` | `Node`, `Fx..Mz`, `Case` | forze [N] e momenti [N m] negli assi globali |
| `DistributedLoad` | `Element`, `Component`, `qi`, `qj`, `a`, `b`, `frame`, `Case` | `Component` = `fx, fy, fz` [N/m] o `mx, my, mz` [N m/m]; `qj` vuoto = uniforme; `a`, `b` = inizio/fine normalizzati del tratto caricato in [0, 1] (vuoti = tutto l'elemento); `frame` = `local` (default) o `global` |
| `ConcentratedLoad` | `Element`, `xi`, `Fx..Mz`, `frame`, `Case` | forza nella posizione normalizzata `xi = x/L` in [0, 1] |

Ogni carico appartiene a un **caso di carico** (colonna `Case`; `default` se
vuota): i nomi dei casi sono le parole che combinerai con `--cases` e nel
foglio `Combination`.

### Thermal, Settlement, Prestress

<div align="center">
  <img src="images/excel_thermal.png" alt="foglio Thermal" width="560">
</div>

| Foglio | Colonne | Note |
|---|---|---|
| `Thermal` | `Element`, `dT_axial`, `dT_grad_y`, `h_y`, `dT_grad_z`, `h_z`, `Case` | variazione uniforme [K] e/o gradienti lineari (differenza di temperatura sull'altezza `h_y` / `h_z` [m]) |
| `Settlement` | `Node`, `Dof`, `Value` | spostamento [m] o rotazione [rad] imposti su `ux..rz`; **sempre attivo**, non ha caso di carico |
| `Prestress` | `Element`, `P`, `e_i`, `e_j`, `plane`, `sag`, `Case` | tiro del cavo [N], eccentricita' agli estremi [m], freccia parabolica (positiva = monta verso l'alto) nel piano `y` o `z`; i carichi equivalenti sono generati automaticamente |

### Combination

<div align="center">
  <img src="images/excel_combination.png" alt="foglio Combination" width="360">
</div>

Una riga per coppia (combinazione, caso di carico): `Name`, `Case`, `Coef`.
Il template contiene `SLU = 1.35 G + 1.5 Q + 0.9 T`, `SLE_rara = G + Q + 0.6 T`
e `SLE_qp = G + 0.3 Q`. Sono le combinazioni eseguite da
`feagent solve --combos` e riportate da `feagent report --combos`.

## 4. Scrivere un modello proprio: una trave appoggiata

Riproduciamo a mano l'esempio `simply_supported`. Parti da un template vuoto
(`feagent template trave.xlsx --blank`) e inserisci le righe seguenti.

**Node** - trave di 6 m in sei elementi da 1 m:

| Node | X | Y | Z |
|---|---|---|---|
| 1 | 0 | 0 | 0 |
| 2 | 1 | 0 | 0 |
| 3 | 2 | 0 | 0 |
| 4 | 3 | 0 | 0 |
| 5 | 4 | 0 | 0 |
| 6 | 5 | 0 | 0 |
| 7 | 6 | 0 | 0 |

**Material** e **Section** (acciaio, una sezione tipo IPE):

| Material | E | nu | alpha | rho |
|---|---|---|---|---|
| S355 | 210e9 | 0.3 | 1.2e-5 | 7850 |

| Section | A | Iy | Iz | J |
|---|---|---|---|---|
| IPE | 1.2e-2 | 3e-5 | 5e-5 | 2e-5 |

**Element** - sei elementi in fila:

| Element | NodeI | NodeJ | Material | Section |
|---|---|---|---|---|
| 1 | 1 | 2 | S355 | IPE |
| 2 | 2 | 3 | S355 | IPE |
| 3 | 3 | 4 | S355 | IPE |
| 4 | 4 | 5 | S355 | IPE |
| 5 | 5 | 6 | S355 | IPE |
| 6 | 6 | 7 | S355 | IPE |

**Support** - cerniera al nodo 1, carrello al nodo 7 (gradi di liberta'
fuori piano `Dz` e `Rx` vincolati a entrambe le estremita'):

| Node | Dx | Dy | Dz | Rx | Ry | Rz |
|---|---|---|---|---|---|---|
| 1 | 1 | 1 | 1 | 1 | 0 | 0 |
| 7 | 0 | 1 | 1 | 1 | 0 | 0 |

**DistributedLoad** - 10 kN/m verso il basso su ogni elemento, caso `G`
(sei righe, una per elemento):

| Element | Component | qi | qj | a | b | frame | Case |
|---|---|---|---|---|---|---|---|
| 1 | fy | -10000 | | | | local | G |
| ... | fy | -10000 | | | | local | G |
| 6 | fy | -10000 | | | | local | G |

**NodalLoad** - 20 kN verso il basso in mezzeria, caso `Q`:

| Node | Fx | Fy | Fz | Mx | My | Mz | Case |
|---|---|---|---|---|---|---|---|
| 4 | 0 | -20000 | 0 | 0 | 0 | 0 | Q |

**Combination** (facoltativo):

| Name | Case | Coef |
|---|---|---|
| SLU | G | 1.35 |
| SLU | Q | 1.5 |
| SLE_rara | G | 1.0 |
| SLE_rara | Q | 1.0 |

Salva e chiudi il file (Excel lo tiene bloccato finche' e' aperto: feagent
puo' leggerlo ma non sovrascriverlo).

## 5. Validare prima di risolvere

```bash
feagent check trave.xlsx --solve
```

<div align="center">
  <img src="images/cli_check.png" alt="feagent check --solve" width="640">
</div>

`check` intercetta gli errori facili da commettere in un foglio di calcolo:
un numero di nodo inesistente, il nome di una sezione scritto male, un
elemento con nodi coincidenti, il foglio `Support` mancante, la posizione di
un carico fuori da `[0, 1]`, una combinazione che cita un caso di carico non
usato da alcun carico. Ogni rilievo porta un codice, il foglio e la riga
Excel, cosi' la correzione e' a un clic di distanza:

<div align="center">
  <img src="images/cli_check_errors.png" alt="feagent check su un workbook con errori" width="640">
</div>

`--solve` esegue anche un'analisi di prova con tutti i carichi e verifica che
la matrice di rigidezza sia ben condizionata (un meccanismo, per esempio una
trave libera di scorrere assialmente, viene segnalato come `E61`) e che le
reazioni equilibrino i carichi applicati. Il codice di uscita e' `4` quando
ci sono errori, il che rende `check` un cancello naturale negli script.

## 6. Ispezionare il modello

```bash
feagent info trave.xlsx
```

<div align="center">
  <img src="images/cli_info.png" alt="feagent info" width="640">
</div>

Ottieni i conteggi, il bounding box, le tabelle di materiali e sezioni e,
cosa piu' utile, i **carichi per caso di carico** e le combinazioni trovate
nel workbook: un modo rapido per confermare che ogni carico e' finito nel
caso voluto.

## 7. Risolvere una combinazione

```bash
feagent solve trave.xlsx --cases G --print
```

<div align="center">
  <img src="images/cli_solve.png" alt="feagent solve --print" width="640">
</div>

La console mostra lo spostamento massimo e il suo nodo, la somma delle
reazioni e, con `--print`, le tabelle di spostamenti (i nodi piu' spostati,
o quelli indicati con `--nodes`) e reazioni. Il workbook
`trave_results.xlsx` contiene i fogli `Displacements`, `Reactions`,
`ElementEndForces` e, con `--n-diagram 21`, `InternalForces` con `N`, `Vy`,
`Vz`, `T`, `My`, `Mz` in 21 stazioni per elemento.

Per la trave qui sopra, il caso `G` da' al nodo 4 `uy = 5 q L^4 / 384 E I =
-16,07 mm`, reazioni `Fy = 30 kN` ai due appoggi e `Mz = 45 kN m` in
mezzeria; il caso `Q` da' `uy = P L^3 / 48 E I = -8,57 mm`. Per rileggere un
file di risultati in seguito:

```bash
feagent results trave_results.xlsx --top 5 --element 3
```

<div align="center">
  <img src="images/cli_results.png" alt="feagent results" width="640">
</div>

## 8. Combinazioni e inviluppi

```bash
feagent solve trave.xlsx --combos --envelope
```

<div align="center">
  <img src="images/cli_solve_combos.png" alt="feagent solve --combos --envelope" width="640">
</div>

Ogni combinazione del foglio `Combination` viene risolta con un'unica
fattorizzazione e scritta nel proprio workbook (`trave_SLU.xlsx`,
`trave_SLE_rara.xlsx`); `--envelope` aggiunge `trave_envelope.xlsx`, i cui
fogli danno minimo e massimo di ogni spostamento, reazione e azione interna
insieme alla **combinazione governante** e, per le azioni interne, alla
posizione lungo l'elemento. Le combinazioni si possono anche scrivere
inline, senza toccare il workbook:

```bash
feagent solve trave.xlsx --combo SLU --combo Vento=G:1.0,W:1.5 --envelope -o out/
```

## 9. Figure

```bash
feagent plot trave.xlsx --what deformed --cases G=1.35 Q=1.5 --open   # interattiva, nel browser
feagent plot trave.xlsx --what forces --component Mz --png            # PNG statico
feagent plot trave.xlsx --what all --cases G Q                        # tutto in trave_figs/
```

<div align="center">
  <img src="images/cli_plot_model.png" alt="grafico del modello" width="270">
  <img src="images/cli_plot_deformed.png" alt="grafico della deformata" width="270">
  <img src="images/cli_plot_forces.png" alt="grafico delle sollecitazioni" width="270">
</div>

## 10. Analisi modale e di buckling

```bash
feagent modal trave.xlsx -n 6 --mass-cases G=1.0 Q=0.3 -o trave_modale.h5
feagent buckling portal_frame.xlsx --cases G
```

Le masse dell'analisi modale vengono dai casi di carico scelti (`rho` dei
materiali e' usato per la massa propria); l'analisi di buckling richiede una
combinazione che metta in sforzo normale gli elementi (un portale sotto
gravita', non una trave caricata trasversalmente).

## 11. Relazione ed export

```bash
feagent report trave.xlsx --combos -o trave.docx --title "Trave appoggiata"
feagent export trave.xlsx trave.tcl            # OpenSees; .s2k SAP2000, .mct MIDAS, .str Robot
```

La relazione Word descrive il modello, mostra i carichi di ogni caso e i
risultati di ogni combinazione con i diagrammi; l'export scrive il modello
per un solutore esterno con gli stessi assi locali e rilasci.

## 12. Automatizzare

- **In serie**: `feagent check "modelli/*.xlsx"` e
  `feagent solve "modelli/*.xlsx" --outdir risultati/` elaborano un'intera
  cartella (i pattern vengono espansi dalla CLI, quindi funzionano anche in
  PowerShell).
- **JSON**: aggiungi `--json` a `doctor`, `check`, `info`, `solve`, `modal`,
  `buckling`, `results`, `examples` per un output leggibile da script,
  notebook o agenti AI (vedi [38 - Server MCP](it-38-mcp-server.html) per
  l'integrazione lato agente).
- **Codici di uscita**: `0` successo, `2` file non trovato, `3` pacchetto
  opzionale mancante, `4` validazione fallita:
  `feagent check m.xlsx && feagent solve m.xlsx --combos`.
- **Completamento**: `feagent completion powershell >> $PROFILE`
  (o `bash` / `zsh`) per completare comandi e opzioni con <kbd>Tab</kbd>.

## 13. Risoluzione dei problemi

| Sintomo | Causa e rimedio |
|---|---|
| `'feagent' non e' riconosciuto` | la cartella `Scripts` di Python non e' sul PATH: esegui `python -m feagent doctor`, che indica la cartella da aggiungere, oppure continua a usare `python -m feagent ...` |
| `ImportError: ... pandas` / codice di uscita 3 | l'extra non e' installato: `pip install "feagent[excel]"` (o `[all]`) |
| `E36 nessun vincolo` | il foglio `Support` manca o tutti i flag sono 0 |
| `E37 nodo non collegato` | un nodo e' definito ma nessun elemento lo usa: rimuovilo o vincolane tutti e sei i gradi di liberta' |
| `E61 rigidezza singolare` | meccanismo: una trave libera di scorrere assialmente, una catena di cerniere, un telaio 3D senza vincoli fuori piano; controlla `Support` e i rilasci |
| spostamenti enormi (`W07`) | unita' incoerenti (mm con Pa, kN con m), una sezione mille volte troppo piccola, oppure un meccanismo |
| `E26 shear = 1 senza Asy/Asz` | gli elementi Timoshenko richiedono le aree di taglio nel foglio `Section` |
| `combinazione ... non trovata` | `--combo NOME` deve coincidere con un nome del foglio `Combination` o con un caso di carico; usa `NOME=CASO:COEF,...` per definirla inline |
| `modal richiede --mass-cases` | le masse vengono dai casi di carico: `--mass-cases G=1.0 Q=0.3` |
| `PermissionError` in scrittura | il workbook e' aperto in Excel: chiudilo o scrivi altrove con `-o` / `--outdir` |
| niente colori / glifi strani | usa Windows Terminal o un font con copertura Unicode (Cascadia); `--color never` per un output semplice |

## Per approfondire

- [11 - Excel I/O](it-11-excel-io.html): il riferimento del formato e le
  funzioni Python dietro la CLI (`read_excel`, `read_combinations`, `write_template`, `write_envelope`).
- [07 - Load case e combinazioni](it-07-load-cases.html) e
  [13 - Convenzioni](it-13-conventions.html): segni, assi e regole di combinazione.
- [22 - Salvataggio risultati](it-22-saving-results.html) e
  [23 - Formato HDF5](it-23-hdf5-format.html): cosa contengono i file di risultati.
- [37 - Interfaccia a riga di comando](it-37-cli.html): ogni comando e opzione.
