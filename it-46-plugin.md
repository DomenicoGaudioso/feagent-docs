---
layout: default
title: "46 - Plugin e strumenti"
parent: Italiano
nav_order: 46
---

# 46 - Plugin e strumenti

I **plugin** aggiungono a feagent strumenti specialistici (generatori di modelli,
analisi dedicate, gemello digitale) che usano **il motore di calcolo di feagent**:
nessun plugin porta con sé un solutore proprio e nessuno richiede OpenSees.

## Strumenti disponibili

| Id | Strumento | Cosa fa |
|---|---|---|
| `bridge` | Modello FEM di ponti | genera il modello analitico di impalcati misti (cassone, due travi, graticcio) e in c.a. a partire dalla descrizione del ponte in Excel o da parametri |
| `train` | Treno su ponte | transito di un convoglio ferroviario su una trave, scansione di velocità, accelerazioni, velocità critica, interazione veicolo struttura |
| `frames` | Telai | telaio piano, telaio spaziale con azioni NTC 2018 e telaio con nucleo in c.a. |
| `trussgirder` | Travi reticolari | generazione parametrica (Warren, Howe, Pratt, ...), analisi statica, modale e pushover |
| `digitaltwin` | Gemello digitale | confronto fra FEM e frequenze misurate, sensibilità di Sobol, calibrazione del modello |

Ogni strumento ha la sua pagina con parametri, formule e validazione.

## Come si usano

**Interfaccia grafica.** Menu *Plugin*: si sceglie lo strumento e l'azione, si
compila il modulo (costruito dai parametri dichiarati dal plugin) e si esegue.
Se l'azione produce un modello, questo si apre nella finestra con casi di carico
e combinazioni; tabelle e grafici compaiono in una finestra di risultati.

**Riga di comando.**

```bash
feagent plugin list
feagent plugin info frames
feagent plugin run frames telaio_piano --param n_piani=3 -o telaio.xlsx
```

`--params-json '{"luce": 12}'` passa i parametri come JSON. Il modello prodotto
va nel workbook indicato da `-o`, le tabelle in un secondo file accanto.

**Libreria.**

```python
from feagent import plugins
res = plugins.get("frames").run("telaio_piano", n_piani=3)
res.model.solve()
```

**Assistenti IA.** I tool `plugin_list`, `plugin_info` e `plugin_run` (MCP, REST,
function calling) descrivono e lanciano qualsiasi plugin; `gui_plugin_run` apre
il modello prodotto nell'interfaccia aperta.

## Scrivere un plugin

Un plugin è un modulo con una variabile `PLUGIN`:

```python
from feagent import Model, Material, Section
from feagent.plugins import Plugin, Action, Param, Result

def genera(luce: float = 10.0) -> Result:
    m = Model()
    ...                                   # costruzione con il motore di feagent
    return Result(message="ok", model=m)  # la GUI lo apre, la CLI lo salva

PLUGIN = Plugin(
    id="esempio", title="Esempio", summary="Una trave appoggiata",
    actions=[Action("genera", "Genera la trave", genera, kind="genera",
                    params=[Param("luce", "Luce", "number", 10.0, unit="m")])])
```

I plugin vengono cercati fra quelli distribuiti con feagent, fra i pacchetti con
entry point `feagent.plugins` e fra i file `.py` della cartella
`~/.feagent/plugins` (o di `FEAGENT_PLUGINS_DIR`). Un plugin che non si importa
non rompe feagent: resta in elenco con il motivo.

Tipi di parametro: `number`, `int`, `text`, `choice`, `bool`, `file`, `json`,
`table`. Un'azione restituisce un `Result` con `model`, `combinations`, `tables`,
`charts`, `files`, `data`, `warnings`.
