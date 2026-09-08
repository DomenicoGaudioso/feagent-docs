---
layout: default
title: "01 - Installazione"
parent: Italiano
nav_order: 1
---

# 01 - Installazione

feagent e' un pacchetto Python puro: gira ovunque giri Python 3.9 o
successivo (Windows, macOS, Linux) e per il solutore richiede soltanto
**numpy** e **scipy**. Tutto il resto (workbook Excel, grafici, relazioni
Word, risultati HDF5, server MCP) e' un *extra* opzionale da installare solo
se serve.

## Requisiti

| | Minimo |
|---|---|
| Python | 3.9 (testato fino a 3.13) |
| numpy | 1.24 |
| scipy | 1.10 |

## 1. Creare un ambiente virtuale (consigliato)

```bash
python -m venv .venv
.venv\Scripts\activate           # Windows (PowerShell / cmd)
source .venv/bin/activate        # macOS / Linux
python -m pip install --upgrade pip
```

## 2. Installare il pacchetto

Il codice sorgente vive in una repository privata; il pacchetto viene
distribuito come **wheel** (`feagent-<versione>-py3-none-any.whl`) oppure come
clone per chi ha accesso.

**Dal wheel** (l'extra `[all]` porta con se' Excel, grafici, relazione, HDF5 e MCP):

```bash
pip install "feagent[all] @ file:///C:/Download/feagent-0.6.0-py3-none-any.whl"    # Windows
pip install "feagent[all] @ file:///home/utente/feagent-0.6.0-py3-none-any.whl"   # macOS / Linux
```

(`pip install feagent-0.6.0-py3-none-any.whl` installa il solo pacchetto
base; gli extra si aggiungono dopo con `pip install pandas openpyxl plotly
matplotlib python-docx h5py`.)

**Da un clone** (sviluppo, installazione editabile):

```bash
git clone https://github.com/DomenicoGaudioso/feagent.git
cd feagent
pip install -e ".[all]"
```

**Direttamente dalla repository** (serve l'accesso):

```bash
pip install "feagent[all] @ git+https://github.com/DomenicoGaudioso/feagent.git"
```

**Con pipx**, per avere il comando `feagent` isolato dagli altri progetti:

```bash
pipx install "feagent[all] @ file:///C:/Download/feagent-0.6.0-py3-none-any.whl"
```

## Extra

| Extra | Pacchetti | Serve per |
|-------|-----------|-----------|
| `excel` | pandas, openpyxl | modelli e risultati Excel (tutto il flusso della CLI) |
| `plot` | plotly, kaleido | figure HTML interattive (`feagent plot`, `feagent.plotting`) |
| `matplotlib` | matplotlib | figure PNG statiche (`plot --png`) |
| `report` | matplotlib, python-docx | relazione di calcolo Word (`feagent report`) |
| `hdf5` | h5py | risultati in HDF5 (`.h5`) |
| `fast` | pypardiso | solutore sparso MKL Pardiso per modelli molto grandi |
| `mcp` | mcp | server MCP per agenti AI (`feagent mcp`) |
| `all` | tutto quanto sopra tranne `fast` | |
| `dev` | `all` + pytest | esecuzione della suite di test |

Gli extra si possono combinare: `pip install "feagent[excel,plot]"` (nella
forma `@ file:///...` o `-e .` vista sopra).

## 3. Verificare l'installazione

```bash
feagent doctor
```

`doctor` elenca l'interprete Python, ogni pacchetto opzionale con l'extra che
lo installa, se i comandi `feagent` e `fg` sono sul PATH, le capacita' del
terminale e l'esito di un auto-test del solutore. In alternativa:

```bash
feagent --version
python -c "import feagent; print(feagent.__version__)"    # 0.6.0
```

Poi esegui la prima analisi da un esempio distribuito:

```bash
feagent examples cantilever --verify
```

## 4. Completamento della shell (facoltativo)

```bash
feagent completion powershell >> $PROFILE     # PowerShell
feagent completion bash >> ~/.bashrc          # bash
feagent completion zsh  >> ~/.zshrc           # zsh
```

## Aggiornare e rimuovere

```bash
pip install --upgrade "feagent[all] @ file:///C:/Download/feagent-0.7.0-py3-none-any.whl"
pip install -e ".[all]"      # in un clone, dopo git pull (da ripetere solo se cambiano le dipendenze)
pip uninstall feagent
```

## Esecuzione dei test (da un clone)

```bash
pip install -e ".[dev]"
python -m pytest tests -q
```

## Risoluzione dei problemi

### `'feagent' non e' riconosciuto come comando` (Windows) o `command not found`

La cartella `Scripts` (Windows) o `bin` (macOS/Linux) dell'ambiente Python
non e' sul PATH. Attiva l'ambiente virtuale, aggiungi al PATH la cartella
indicata da `python -m feagent doctor` ("Cartella script"), oppure richiama
semplicemente il modulo:

```bash
python -m feagent doctor
```

### `ImportError: ... pandas` / `plotly` / `docx` (codice di uscita 3)

L'extra corrispondente non e' installato. Il messaggio di errore indica
l'extra; `feagent doctor` stampa la riga completa `pip install "feagent[...]"`.

### Colori o caratteri di cornice sbagliati

Usa Windows Terminal (o un terminale con colore a 24 bit e UTF-8) e un font
con buona copertura Unicode come Cascadia Code / Mono. `feagent --color never`
da' un output semplice; `NO_COLOR=1` fa lo stesso per ogni esecuzione.

### `ValueError: ref_vector parallelo all'asse della trave`

Per gli elementi verticali (lungo Y) non usare un vettore di riferimento
parallelo all'asse; lascialo vuoto (il default sceglie X globale per gli
elementi verticali) oppure usa `RefX = 1`. Vedi
[08 - Orientazione della sezione](it-08-section-orientation.html).

### Il file Excel non si puo' scrivere (`PermissionError`)

Il workbook e' aperto in Excel: chiudilo, oppure scrivi i risultati altrove
con `-o` / `--outdir`.

## Prossimi passi

- [02 - Quick Start](it-02-quick-start.html): il primo modello con l'API Python.
- [41 - Tutorial CLI](it-41-cli-excel-tutorial.html): il primo modello da un workbook Excel, senza codice.
- [37 - Interfaccia a riga di comando](it-37-cli.html): riferimento di ogni comando.
