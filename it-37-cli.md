---
layout: default
title: "37 - Interfaccia a riga di comando (CLI)"
parent: Italiano
nav_order: 37
---

# 37 - Interfaccia a riga di comando (CLI)

L'installazione del pacchetto registra una **interfaccia a riga di comando**
con cui creare, validare, analizzare, visualizzare ed esportare i modelli
direttamente dal terminale, **senza scrivere codice Python**. Il modello vive
in un workbook Excel (vedi [11 - Excel I/O](it-11-excel-io.html)); la CLI lo
legge, esegue l'analisi e scrive risultati, figure e relazioni accanto ad esso.

Tre modi equivalenti per richiamarla:

```bash
feagent <comando> ...          # il comando installato
fg <comando> ...               # alias breve
python -m feagent <comando>    # funziona sempre, anche se Scripts/ non e' sul PATH
```

<div align="center">
  <img src="images/cli_snake_anim.gif" alt="logo animato della CLI feagent" width="440">
</div>

{: .note }
Prima volta con la CLI? Segui il [41 - Tutorial CLI](it-41-cli-excel-tutorial.html)
passo passo: va dall'installazione a un modello risolto, foglio per foglio.
Questa pagina e' il **riferimento** di ogni comando e opzione.

## I comandi in breve

| Gruppo | Comando | Cosa fa |
|---|---|---|
| Ambiente | `doctor` | controlla Python, pacchetti opzionali, PATH e terminale; auto-test del solutore |
| | `completion` | stampa lo script di completamento per la shell (bash, zsh, PowerShell) |
| | `version`, `logo` | versione; logo animato |
| Creazione del modello | `examples` | catalogo di modelli Excel pronti, con verifiche analitiche |
| | `template` | scrive un template Excel compilabile (con fogli README e Combination) |
| | `new` | costruisce un piccolo modello con un wizard interattivo |
| | `convert` | traduce le tabelle esportate da SAP2000 / MIDAS / Robot in un workbook feagent |
| Validazione | `check` | valida il workbook (riferimenti, vincoli, carichi, combinazioni), con analisi di prova opzionale |
| | `info` | riepilogo del modello: geometria, materiali, sezioni, carichi per caso, combinazioni |
| Analisi | `solve` | analisi statica di una combinazione, o di tutte quelle del foglio `Combination`, con inviluppi |
| | `modal` | frequenze proprie, periodi e masse partecipanti |
| | `buckling` | moltiplicatori critici di buckling lineare |
| Risultati | `results` | stampa un file di risultati (`.xlsx` o `.h5`): spostamenti, reazioni, forze d'estremita', modi |
| | `plot` | figure HTML interattive (Plotly) o PNG (Matplotlib) di modello, carichi, deformata, diagrammi, reazioni |
| | `report` | relazione di calcolo Word (`.docx`) |
| | `export` | esportazione verso OpenSees, SAP2000, MIDAS, Robot, Straus7 |
| Connettore AI | `connect` | configura un client AI (Claude Desktop/Code, ChatGPT, Codex, Gemini, Copilot, Cursor, ...) con un comando |
| | `mcp` | server MCP per gli agenti AI, stdio o HTTP |
| | `serve` | server REST/OpenAPI per Custom GPT Actions, Open WebUI, n8n |
| | `tools` | definizioni dei tool per il function calling (OpenAI, Anthropic, Gemini, OpenAPI) |

```bash
feagent                 # logo + elenco comandi
feagent --help          # guida completa
feagent solve --help    # guida di un comando
```

## Opzioni globali, variabili d'ambiente e codici di uscita

| Opzione | Effetto |
|---|---|
| `-q`, `--quiet` | solo l'output essenziale (niente intestazione ne' righe di avanzamento); accettata prima **o dopo** il comando |
| `--json` | disponibile su quasi tutti i comandi: stampa **solo JSON** su stdout (i messaggi vanno su stderr), per script e agenti |
| `--color {auto,always,never}` | forza o disabilita i colori ANSI (default: rilevamento automatico) |
| `--no-banner` | nasconde la riga di intestazione |
| `-V`, `--version` | stampa la versione ed esce |

| Variabile d'ambiente | Effetto |
|---|---|
| `NO_COLOR=1` | disabilita i colori (come `--color never`) |
| `FORCE_COLOR=1` | forza i colori anche quando stdout non e' un terminale |
| `FEAGENT_DEBUG=1` | mostra il traceback Python completo in caso di errore |

| Codice di uscita | Significato |
|---|---|
| `0` | successo |
| `1` | errore generico (nome di combinazione inesistente, matrice singolare, ...) |
| `2` | uso errato o file di input non trovato |
| `3` | manca una dipendenza opzionale (il messaggio dice quale `pip install "feagent[...]"` la installa) |
| `4` | validazione fallita (`check`) o verifica analitica fallita (`examples --verify`) |
| `130` | interrotto con Ctrl-C |

## Combinazioni di carico: `--cases` e `--combo`

Ogni comando di analisi accetta una **singola combinazione** con `--cases`:

| Forma | Significato |
|---|---|
| *(omesso)* | tutti i carichi, coefficiente 1 |
| `--cases G` | un singolo caso di carico |
| `--cases G Q` | combinazione, coefficiente 1 ciascuno |
| `--cases G=1.35 Q=1.5` | combinazione con coefficienti moltiplicativi |

`solve` e `report` lavorano anche su **combinazioni con nome**, memorizzate
nel foglio `Combination` del workbook (una riga per coppia
combinazione/caso: `Name`, `Case`, `Coef`) oppure date inline:

| Forma | Significato |
|---|---|
| `--combos` | tutte le combinazioni del foglio `Combination` |
| `--combo SLU` | la combinazione `SLU` del foglio (o il solo caso di carico `SLU`) |
| `--combo SLU=G:1.35,Q:1.5` | definizione inline (coppie `CASO:COEF`, ammesso anche `CASO=COEF`, senza coefficiente = 1); ripetibile |
| `--envelope` | con piu' combinazioni: scrive anche il workbook dell'inviluppo min/max |

## `doctor` - controllo dell'ambiente

```bash
feagent doctor            # rapporto leggibile
feagent doctor --json     # stessi dati per gli script
```

Stampa versione e percorso di feagent e di Python, una tabella di ogni
pacchetto con l'extra che lo installa (`feagent[excel]`, `feagent[plot]`,
...), la riga `pip install` per cio' che manca, se i comandi `feagent` / `fg`
sono sul PATH (altrimenti il ripiego `python -m feagent`), le capacita' del
terminale (colori ANSI, Unicode, encoding) e un auto-test del solutore
(mensola `P L^3 / 3 E I`). Codice di uscita `1` se manca un pacchetto
obbligatorio o l'auto-test fallisce.

<div align="center">
  <img src="images/cli_doctor.png" alt="feagent doctor" width="640">
</div>

## `examples` - modelli Excel pronti

```bash
feagent examples                            # catalogo
feagent examples cantilever                 # scrive cantilever.xlsx nella cartella corrente
feagent examples cantilever -o mensola.xlsx --lang it
feagent examples simply_supported --verify  # scrive, risolve e confronta con le formule chiuse
feagent examples --all -o esempi/           # tutti i modelli in una cartella
feagent examples --json                     # catalogo in JSON
```

Ogni esempio e' un workbook completo (con il foglio `Combination` dove ha
senso) e porta con se' delle **verifiche in forma chiusa**: `--verify`
rilegge il file, lo risolve e stampa valori attesi e calcolati con l'errore
relativo; codice di uscita `4` se una verifica fallisce.

| Chiave | Modello | Feature del formato Excel mostrate | Verifiche |
|---|---|---|---|
| `cantilever` | mensola, carico in punta (Q) + permanente (G) | NodalLoad, DistributedLoad, Combination | `P L^3/3EI`, `P L^2/2EI`, `q L^4/8EI`, reazioni, `M` all'incastro (SLU) |
| `simply_supported` | trave di 6 m, 6 elementi, carico uniforme + forza in mezzeria | cerniera/carrello, casi di carico | `5 q L^4/384EI`, `P L^3/48EI`, `q L/2`, `q L^2/8`, `P L/4` |
| `continuous_beam` | tre campate 6 + 6 + 8 m, cedimento di un appoggio interno | foglio Settlement | somma delle reazioni per caso, spostamento imposto |
| `portal_frame` | portale piano, colonne HEB300 + trave IPE400, vento | `RefX/RefY/RefZ`, due sezioni, tre combinazioni | equilibrio globale per caso e combinazione |
| `frame_3d` | telaio spaziale a un piano 6 x 4 m | geometria 3D, `RefX` sulle colonne | equilibrio globale |
| `grillage` | due travi principali + sette traversi (impalcato) | graticcio orizzontale X-Z, carico di ruota | equilibrio globale |
| `thermal` | asta incastrata alle due estremita', +30 K | foglio Thermal | `N = E A alpha dT`, spostamento nullo |
| `prestress` | trave di 20 m con cavo parabolico, elemento per elemento | foglio Prestress (`e_i`, `e_j`, `sag`) | monta `5 w L^4/384EI` con `w = 8 P s/L^2`, `M = P s`, `N = P`, reazioni nulle |
| `timoshenko` | mensola tozza in c.a. | `shear = 1`, `Asy`/`Asz` | `P L^3/3EI + P L/G As` |
| `hinges` | trave Gerber con cerniera interna | `ReleasesJ = rz` | reazioni, `M` all'incastro, `M = 0` alla cerniera |

<div align="center">
  <img src="images/cli_examples.png" alt="feagent examples" width="640">
</div>

<div align="center">
  <img src="images/cli_examples_verify.png" alt="feagent examples simply_supported --verify" width="640">
</div>

## `template` - template Excel

```bash
feagent template modello.xlsx              # mensola a 2 elementi con un carico di ogni tipo
feagent template modello.xlsx --blank      # sole intestazioni di colonna, senza righe di esempio
feagent template modello.xlsx --no-readme --no-combos
```

Il workbook contiene un foglio per tipo di dato (`Node`, `Material`,
`Section`, `Element`, `Support`, `NodalLoad`, `DistributedLoad`,
`ConcentratedLoad`, `Thermal`, `Settlement`, `Prestress`), un foglio
`Combination` con esempi SLU/SLE e un foglio `README` che descrive ogni
colonna, unita' e convenzione (in inglese e in italiano). `README` viene
ignorato in lettura. Il formato e' documentato in [11 - Excel I/O](it-11-excel-io.html).

## `new` - wizard guidato

`feagent new modello.xlsx` chiede nodi, materiale, sezione, elementi, vincoli
e carichi nodali e scrive il workbook (premi <kbd>Invio</kbd> per accettare
il default, lascia una riga vuota per chiudere una lista). I vincoli si
inseriscono come `nodo dofs` con sei cifre 0/1 (`1 111111` = incastro), i
carichi come `nodo Fx Fy Fz Mx My Mz [Case]`.

## `convert` - importare tabelle esterne

```bash
feagent convert tabelle_sap.xlsx modello.xlsx --A 1e-2 --Iy 2e-5 --Iz 3e-5 --J 1e-5
```

Riconosce i nomi di foglio e colonna esportati abitualmente da SAP2000
(`Joint Coordinates`, `Connectivity - Frame`, `Joint Restraint Assignments`,
`Joint Loads - Force`, ...), MIDAS (`NODE`, `ELEMENT`, `CONSTRAINT`,
`CONLOAD`) e Robot (`Nodes`, `Bars`, `Supports`) e scrive un workbook feagent.
Dove il file esterno non ha dati numerici di materiale/sezione si usano i
default `--E`, `--nu`, `--A`, `--Iy`, `--Iz`, `--J` (e i nomi `--material`,
`--section`): rivedili, poi `check` e `solve`.

## `check` - validare il workbook

```bash
feagent check modello.xlsx                 # controlli strutturali
feagent check modello.xlsx --solve         # + analisi di prova (condizionamento, equilibrio)
feagent check "modelli/*.xlsx" --strict    # in serie; gli avvisi contano come errori
feagent check modello.xlsx --json
```

Controlla il workbook **prima** di costruire il modello e riporta ogni
rilievo con un codice stabile, il foglio e la **riga Excel** (intestazione =
riga 1):

| Gruppo | Codici | Cosa intercetta |
|---|---|---|
| Fogli e colonne | `E01`-`E02`, `E07`-`E08`, `E11`-`E13`, `E16`-`E18` | foglio `Node` mancante, fogli `Material`/`Section`/`Element` mancanti o senza le colonne obbligatorie |
| Id e riferimenti | `E03`-`E04`, `E09`, `E14`, `E19`-`E22`, `E25` | id non interi o duplicati, elementi che puntano a nodi, materiali (avviso `W03`) o sezioni non definiti |
| Geometria | `E23`-`E24`, `E37` | nodi di estremita' coincidenti, elementi di lunghezza nulla, nodi non collegati ad alcun elemento e non completamente vincolati |
| Dati degli elementi | `E26`-`E27` | `shear = 1` su una sezione senza `Asy`/`Asz`; nomi di rilascio non validi |
| Vincoli | `E29`-`E36`, `W04` | vincolo su nodo inesistente, nessun grado di liberta' vincolato (struttura labile), righe tutte a zero |
| Carichi | `E38`-`E56`, `W05` | carichi su nodi/elementi inesistenti, `Component` non valida, `a`/`b`/`xi` fuori da `[0, 1]`, gradiente termico senza `h_y`/`h_z`, `Dof`, `plane` o `frame` non validi |
| Combinazioni | `E57`-`E59` | colonne mancanti, coefficiente non numerico, combinazione che cita un caso di carico non usato da alcun carico |
| Costruzione del modello | `E60` | qualsiasi errore sollevato costruendo il modello |
| Analisi di prova (`--solve`) | `E61`-`E63`, `W07` | rigidezza singolare o quasi (meccanismo), analisi fallita, residuo dell'equilibrio globale, spostamenti molto maggiori del modello |
| Informazioni | `I01`-`I03`, `W01`-`W02`, `W06` | fogli sconosciuti (ignorati), colonne di coordinate mancanti, nessun carico |

Codice di uscita `4` in presenza di errori (o di avvisi con `--strict`): il
comando puo' fare da cancello in un batch o in una pipeline.

<div align="center">
  <img src="images/cli_check.png" alt="feagent check --solve" width="640">
</div>

<div align="center">
  <img src="images/cli_check_errors.png" alt="feagent check su un workbook con errori" width="640">
</div>

## `info` - riepilogo del modello

```bash
feagent info modello.xlsx
feagent info modello.xlsx --json
```

Nodi, elementi (Timoshenko e con rilasci contati a parte), molle, gusci,
cavi; bounding box e lunghezza totale delle travi; tabelle di materiali e
sezioni; carichi per tipo e **per caso di carico**; le combinazioni del
foglio `Combination`.

<div align="center">
  <img src="images/cli_info.png" alt="feagent info" width="640">
</div>

## `solve` - analisi statica

```bash
feagent solve modello.xlsx                                  # tutti i carichi, coeff. 1 -> modello_results.xlsx
feagent solve modello.xlsx --cases G=1.35 Q=1.5 -o slu.xlsx
feagent solve modello.xlsx --cases G Q --print --top 5      # tabelle di spostamenti e reazioni
feagent solve modello.xlsx --cases Q --nodes 3 7            # spostamenti dei nodi scelti
feagent solve modello.xlsx --combos --envelope              # tutte le combinazioni del foglio + inviluppo
feagent solve modello.xlsx --combo SLU --combo X=G:1,Q:2 -o out/
feagent solve "modelli/*.xlsx" --outdir risultati/          # in serie
feagent solve modello.xlsx --format hdf5 --n-diagram 21     # HDF5 con i diagrammi delle azioni interne
feagent solve modello.xlsx --client                         # tabulato cliente esteso
feagent solve modello.xlsx --cases Q --json
```

| Opzione | Significato |
|---|---|
| `input` | uno o piu' workbook; i pattern come `*.xlsx` vengono espansi dalla CLI (utile su Windows) |
| `-o`, `--output` | file di output per una singola analisi; con piu' combinazioni e' la **cartella** di destinazione |
| `--outdir` | cartella di destinazione (creata se manca); obbligatoria con piu' file di input |
| `--format {excel,hdf5}` | formato quando `-o` non e' indicato (`<nome>_results.xlsx` o `.h5`) |
| `--cases`, `--combos`, `--combo`, `--envelope` | vedi [combinazioni di carico](#combinazioni-di-carico---cases-e---combo) |
| `--print`, `--nodes N ...`, `--top N` | stampa le tabelle di spostamenti e reazioni (gli `N` nodi piu' spostati, o quelli scelti) |
| `--sparse` / `--dense` | forza il solutore; di default sparso oltre 3000 gradi di liberta' |
| `--client` | tabulato cliente esteso (modello, carichi, risultati e diagrammi in un unico file, solo Excel) |
| `--n-diagram N` | stazioni per elemento nel foglio `InternalForces` (0 = nessuna; 21 nell'inviluppo) |
| `--json` | riepilogo (spostamento massimo, nodi piu' spostati, reazioni totali, file scritti) in JSON |

Il workbook dei risultati ha i fogli `Displacements`, `Reactions`,
`ElementEndForces` e, con `--n-diagram`, `InternalForces` (vedi
[22 - Salvataggio risultati](it-22-saving-results.html)). Con piu'
combinazioni si scrive un file per combinazione (`modello_SLU.xlsx`,
`modello_SLE_rara.xlsx`, ...) e la console mostra una tabella di confronto;
`--envelope` aggiunge `modello_envelope.xlsx` con i fogli `Summary`,
`Displacements` e `Reactions` (min/max per nodo e GdL con la combinazione
governante) e `InternalForces` (min/max di `N`, `Vy`, `Vz`, `T`, `My`, `Mz`
lungo ogni elemento, con combinazione e ascissa).

<div align="center">
  <img src="images/cli_solve.png" alt="feagent solve --print" width="640">
</div>

<div align="center">
  <img src="images/cli_solve_combos.png" alt="feagent solve --combos --envelope" width="640">
</div>

## `results` - leggere un file di risultati

```bash
feagent results modello_SLU.xlsx --top 5
feagent results modello_SLU.xlsx --node 3 7 --element 2
feagent results modale.h5                   # modi / moltiplicatori critici se presenti
feagent results modello_results.xlsx --json
```

Stampa i nodi piu' spostati (o quelli scelti), i nodi con reazione non
nulla e la loro somma, le forze d'estremita' degli elementi scelti (assi
locali) e, per i file HDF5, le tabelle modali e di buckling.

<div align="center">
  <img src="images/cli_results.png" alt="feagent results" width="640">
</div>

## `modal` - frequenze proprie

```bash
feagent modal modello.xlsx -n 12 --mass-cases G=1.0 Q=0.3 -o modale.h5
feagent modal modello.xlsx -n 5 --mass-cases G --json
```

Le masse derivano dai casi di carico scelti (`--mass-cases` e'
obbligatorio); la tabella riporta frequenza, periodo e masse partecipanti per
modo. `-o` salva i modi in HDF5 (leggibile con `results`).

<div align="center">
  <img src="images/cli_modal.png" alt="feagent modal" width="640">
</div>

## `buckling` - buckling lineare

```bash
feagent buckling modello.xlsx -n 4 --cases G Q
feagent buckling modello.xlsx --cases G --json
```

Moltiplicatori critici della combinazione di riferimento, che deve produrre
sforzo normale in almeno un elemento (una mensola caricata solo
trasversalmente non ha modi di buckling: il comando lo spiega ed esce con
codice `1`).

## `plot` - figure

```bash
feagent plot modello.xlsx --what model --open                          # HTML interattivo nel browser
feagent plot modello.xlsx --what deformed --cases G=1.35 Q=1.5 --scale 200
feagent plot modello.xlsx --what forces --component Mz --png           # PNG statico
feagent plot modello.xlsx --what all --cases G Q -o figure/            # tutte le figure
```

| `--what` | Figura |
|---|---|
| `model` | geometria, numeri dei nodi, vincoli |
| `loads` | carichi della combinazione |
| `deformed` | deformata (scala automatica, o `--scale`) |
| `forces` | diagramma dell'azione interna `--component` (`N`, `Vy`, `Vz`, `T`, `My`, `Mz`; default `Mz`) |
| `reactions` | reazioni vincolari |
| `all` | tutte le figure in una cartella (`<nome>_figs/` di default) |

Di default le figure sono file HTML Plotly interattivi (viene stampato un
link `file://` cliccabile; `--open` apre il browser); `--png` scrive invece
un'immagine statica Matplotlib.

<div align="center">
  <img src="images/cli_plot.png" alt="feagent plot all" width="620">
</div>

<div align="center">
  <img src="images/cli_plot_model.png" alt="grafico del modello" width="270">
  <img src="images/cli_plot_deformed.png" alt="grafico della deformata" width="270">
  <img src="images/cli_plot_forces.png" alt="grafico delle sollecitazioni" width="270">
</div>

## `report` - relazione di calcolo Word

```bash
feagent report modello.xlsx -o relazione.docx --cases G=1.35 Q=1.5
feagent report modello.xlsx -o relazione.docx --combos --title "Portale" --subtitle "Predimensionamento"
feagent report modello.xlsx --no-solve      # sola descrizione del modello
```

Descrizione del modello, una figura per caso di carico, risultati e
diagrammi della combinazione richiesta (con `--combos` / `--combo`, un
capitolo per combinazione con nome). Richiede `python-docx` e `matplotlib`
(`feagent[report]`).

## `export` - solutori esterni

```bash
feagent export modello.xlsx modello.tcl                # formato dall'estensione
feagent export modello.xlsx modello.s2k --format sap2000
```

| Formato | `--format` | Estensione |
|---|---|---|
| OpenSees (Tcl) | `opensees` | `.tcl` |
| OpenSeesPy | `openseespy` | `.py` |
| SAP2000 | `sap2000` | `.s2k` |
| MIDAS Civil / Gen | `midas` | `.mct` |
| Robot | `robot` | `.str` |
| Straus7 | `straus7` | `.txt` |

Vedi [24 - Export verso software esterni](it-24-external-export.html) per la
mappatura di assi locali, rilasci e carichi.

## `completion` - completamento della shell

```bash
feagent completion bash >> ~/.bashrc
feagent completion zsh  >> ~/.zshrc
feagent completion powershell >> $PROFILE       # Windows PowerShell / pwsh
```

Genera uno script che completa nomi dei comandi e opzioni per `feagent` e
`fg` (bash e zsh tramite `compgen`; PowerShell tramite
`Register-ArgumentCompleter`). Riavvia la shell dopo averlo aggiunto.

## Connettore AI - `connect`, `mcp`, `serve`, `tools`

```bash
feagent connect                                   # client configurabili e dove sta la loro configurazione
feagent connect claude-desktop --write            # registra feagent in Claude Desktop (MCP, stdio)
feagent connect cursor --project --url http://127.0.0.1:8765/mcp --api-key SEGRETO
feagent connect chatgpt                           # procedure per ChatGPT (connettore / Custom GPT Actions)
feagent mcp                                       # server MCP su stdio (avviato dal client)
feagent mcp --transport http --port 8765 --root C:/modelli --api-key SEGRETO --allow-any-host
feagent serve --port 8766 --root C:/modelli --api-key SEGRETO   # REST + /openapi.json
feagent tools                                     # tabella dei 17 tool
feagent tools --format openai > tools.json        # oppure anthropic | gemini | json | openapi
```

| Comando | Opzioni |
|---|---|
| `connect [client]` | `--write` (fonde nella configurazione del client con backup `.bak`), `--project` (file di progetto), `--url` / `--api-key` (punta a un server HTTP gia' avviato), `--command python|feagent`, `--root`, `--rest-url`, `--json` |
| `mcp` | `--transport stdio|http|sse`, `--host`, `--port` (8765), `--root` (cartella sandbox), `--api-key` (o `FEAGENT_API_KEY`), `--allow-any-host`, `--stateless` |
| `serve` | `--host`, `--port` (8766), `--root` (default: cartella corrente), `--api-key`, `--cors ORIGINE`, `--no-cors`, `--verbose` |
| `tools` | `--format table|json|openai|anthropic|gemini|openapi`, `--url` e `--secured` per il documento OpenAPI |

Il quadro completo (quale client usa quale canale, i tool, la specifica JSON
del modello, la sicurezza) e' in [38 - Connettore AI](it-38-mcp-server.html).

## `logo`, `version`

`feagent logo` riproduce il logo animato (`--still` fotogramma statico,
`--loop` fino a Ctrl-C, `--image` per il logo pixel-art truecolor,
`--image-variant ibeam` per quello storico). `feagent version` stampa la
versione.

<div align="center">
  <img src="images/cli_logo.png" alt="feagent logo --still" width="520">
</div>

## Script con `--json`

Ogni comando con `--json` stampa su stdout soltanto JSON, quindi si puo'
incanalare in altri programmi:

```bash
feagent solve modello.xlsx --cases G=1.35 Q=1.5 --json > run.json
feagent check "modelli/*.xlsx" --json | python -c "import json,sys; print([r['path'] for r in json.load(sys.stdin) if not r['ok']])"
```

```powershell
# PowerShell: risolve ogni workbook di una cartella e raccoglie lo spostamento massimo
Get-ChildItem modelli\*.xlsx | ForEach-Object {
    $r = feagent solve $_.FullName --cases G=1.35 Q=1.5 --outdir out --json | ConvertFrom-Json
    "{0,-30} {1:E3}" -f $_.Name, $r.u_abs_max
}
```

I codici di uscita (vedi sopra) permettono a un batch di fermarsi al primo
workbook non valido: `feagent check modello.xlsx && feagent solve modello.xlsx --combos`.

## Colori e terminale

L'output e' colorato automaticamente (ANSI a 24 bit). Usa `--color never` o
`NO_COLOR=1` per disabilitarlo, `--color always` / `FORCE_COLOR=1` per
mantenere i colori quando l'output va in un file. Su Windows la CLI abilita
da sola il Virtual Terminal, quindi i colori funzionano in Windows Terminal e
nelle console PowerShell moderne; perche' i caratteri box-drawing e i simboli
siano nitidi usa un font con buona copertura Unicode come **Cascadia Code /
Mono** (il default di Windows Terminal). Dove l'Unicode non e' disponibile la
CLI ricade sui glifi ASCII.

{: .note }
`plot` richiede `plotly` (HTML) o `matplotlib` (`--png`), `report` richiede
`python-docx` e `matplotlib`, l'output HDF5 richiede `h5py`: esegui
`feagent doctor` per vedere cosa e' installato, o installa tutto con
`pip install "feagent[all]"`.
