---
layout: default
title: "38 - Connettore AI (MCP, REST, function calling)"
parent: Italiano
nav_order: 38
---

# 38 - Connettore AI: usare feagent da Claude, ChatGPT, Codex, Gemini, Copilot e da qualsiasi LLM

feagent include un **connettore** con cui gli assistenti AI pilotano il
solutore: validare un workbook, costruire un modello da una descrizione,
eseguire analisi statiche, modali e di buckling, risolvere tutte le
combinazioni con gli inviluppi, guardare la deformata, scrivere la relazione
Word. Un unico registro di **17 tool** (`feagent.agent_api`) alimenta tre
canali, cosi' ogni assistente parla con lo stesso feagent:

| Canale | Comando | Chi lo usa |
|---|---|---|
| **MCP** (Model Context Protocol), stdio o HTTP | `feagent mcp` | Claude Desktop, Claude Code, connettori di ChatGPT, OpenAI Codex CLI, Gemini CLI, GitHub Copilot (VS Code), Cursor, Windsurf, Cline, Continue, OpenAI Agents SDK, LangChain |
| **REST + OpenAPI** | `feagent serve` | Custom GPT Actions, Open WebUI, n8n, Dify, Power Automate, qualsiasi client HTTP |
| **Function calling** (senza server) | `feagent tools`, `feagent.agent_api` | il proprio codice Python con gli SDK OpenAI, Anthropic o Gemini |

```bash
pip install "feagent[mcp]"            # aggiunge l'SDK mcp (Python 3.10+); REST e function calling non richiedono altro
feagent connect                       # quali client si possono configurare e dove sta la loro configurazione
feagent connect claude-desktop --write   # un comando per client
```

{: .note }
Tutto cio' che l'AI puo' fare e' una normale operazione di feagent sui file:
leggere e scrivere workbook, risultati, figure e relazioni. Nessun tool esegue
codice arbitrario. Con `--root` i server sono confinati in una cartella
(sandbox).

## 1. Collegare un client con un comando

`feagent connect <client>` stampa lo snippet di configurazione del client e,
con `--write`, lo fonde nel file di configurazione (viene tenuta una copia
`.bak`). Il comando registrato e' l'interprete Python corrente con
`-m feagent mcp`, cosi' funziona anche quando l'app non vede la cartella
`Scripts`; `--command feagent` registra invece l'eseguibile.

| Client | Comando | Note |
|---|---|---|
| Claude Desktop | `feagent connect claude-desktop --write` | scrive `claude_desktop_config.json`; riavvia l'app, i tool compaiono nel menu degli strumenti |
| Claude Code | `feagent connect claude-code --write` (`.mcp.json` di progetto) oppure `claude mcp add --scope user feagent -- python -m feagent mcp` | HTTP: `claude mcp add --transport http feagent http://127.0.0.1:8765/mcp` |
| ChatGPT | `feagent connect chatgpt` | stampa le due strade: connettore MCP (modalita' sviluppatore, tunnel HTTPS) o Custom GPT Actions sul server REST con chiave API, vedi sotto |
| OpenAI Codex CLI | `feagent connect codex --write` | sezione `[mcp_servers.feagent]` di `~/.codex/config.toml` |
| Gemini CLI | `feagent connect gemini --write` (`--project` per `.gemini/settings.json`) | verifica con `/mcp` dentro Gemini |
| VS Code / GitHub Copilot | `feagent connect vscode --project --write` (`.vscode/mcp.json`) o `mcp.json` utente | Copilot Chat in modalita' Agent, icona degli strumenti |
| Cursor | `feagent connect cursor --write` (`--project` per `.cursor/mcp.json`) | Settings > MCP |
| Windsurf | `feagent connect windsurf --write` | `~/.codeium/windsurf/mcp_config.json` |
| Cline | `feagent connect cline --write` | `cline_mcp_settings.json` nel globalStorage di VS Code |
| Continue | `feagent connect continue` | stampa il blocco YAML per `~/.continue/config.yaml` |
| Open WebUI, n8n, Dify | `feagent connect openwebui` | server REST + `openapi.json` |
| SDK Python | `feagent connect python` | snippet per OpenAI Agents SDK, function calling, LangChain |

Ogni client MCP puo' anche puntare a un **server HTTP gia' avviato** invece
di lanciare un processo: `feagent connect cursor --url
http://127.0.0.1:8765/mcp --api-key SEGRETO`. `feagent connect --json`
restituisce gli stessi dati per gli script.

## 2. Cosa puo' fare l'assistente: i tool

| Tool | Scopo | Scrive file |
|---|---|---|
| `version` | versione, elementi, analisi, cartella sandbox | |
| `excel_format` | fogli/colonne del formato Excel, convenzioni, **specifica JSON del modello** con un esempio completo | |
| `list_examples`, `write_example` | catalogo dei 10 modelli distribuiti; ne scrive uno (con verifica opzionale contro le formule chiuse) | si' |
| `create_template` | template Excel (fogli README e Combination), `blank=True` per le sole intestazioni | si' |
| `model_info` | geometria, materiali, sezioni, carichi per caso, combinazioni | |
| `check_model` | validazione con codici, foglio e riga Excel; `solve=True` aggiunge l'analisi di prova (meccanismi, equilibrio) | |
| `model_to_json` | il workbook come record JSON (inverso di `build_model`) | |
| `build_model` | **modello da una specifica JSON** (nodi, materiali, sezioni, elementi, vincoli, carichi per caso, combinazioni) scritto come workbook e validato | si' |
| `solve_static` | una combinazione: spostamento massimo, nodi piu' spostati, reazioni totali, file di risultati opzionale | opzionale |
| `solve_combinations` | tutte le combinazioni con nome (foglio o inline) con un'unica fattorizzazione, un file di risultati ciascuna, workbook dell'inviluppo ed **estremi di N, V, M con la combinazione governante** | si' |
| `modal_analysis`, `buckling_analysis` | frequenze / moltiplicatori critici, HDF5 opzionale | opzionale |
| `read_results` | spostamenti, reazioni, forze d'estremita', modi da un file di risultati | |
| `plot_model` | PNG di modello, carichi, deformata, diagramma di un'azione interna o reazioni; ai client MCP arriva **come immagine** | si' |
| `export_model` | OpenSees, SAP2000, MIDAS, Robot, Straus7 | si' |
| `create_report` | relazione di calcolo Word (una combinazione o tutte) | si' |

Le combinazioni si scrivono come nella CLI: `"G=1.35 Q=1.5"` (coefficienti)
o `"G Q"` (coefficiente 1). I risultati sono JSON; `plot_model` restituisce il
PNG sia come file sia come `png_base64` (i client MCP lo ricevono come
immagine).

Il server MCP pubblica anche **risorse** che l'assistente puo' leggere prima
di agire: `feagent://format` (il formato e la specifica JSON),
`feagent://examples` e `feagent://examples/{key}` (un esempio con le sue
verifiche analitiche e i fogli); e due **prompt**: `analyze_workbook(path)`
(la sequenza consigliata check → info → solve → plot → sintesi) e
`build_model_from_description(description, output_path)`.

### La specifica JSON del modello (`build_model`)

L'assistente puo' creare un modello senza toccare Excel:

```json
{
  "nodes": [{"id": 1, "x": 0, "y": 0, "z": 0}, {"id": 2, "x": 4, "y": 0, "z": 0}],
  "materials": [{"name": "S355", "E": 210e9, "nu": 0.3, "alpha": 1.2e-5, "rho": 7850}],
  "sections": [{"name": "IPE300", "A": 5.38e-3, "Iy": 6.04e-5, "Iz": 8.36e-4, "J": 2.07e-7}],
  "elements": [{"id": 1, "node_i": 1, "node_j": 2, "material": "S355", "section": "IPE300"}],
  "supports": [{"node": 1, "type": "fixed"}],
  "nodal_loads": [{"node": 2, "Fy": -10000, "case": "Q"}],
  "distributed_loads": [{"element": 1, "component": "fy", "qi": -2000, "case": "G"}],
  "combinations": {"SLU": {"G": 1.35, "Q": 1.5}, "SLE": {"G": 1.0, "Q": 1.0}}
}
```

Le chiavi seguono i fogli Excel ([11 - Excel I/O](it-11-excel-io.html)):
`supports` accetta `type: fixed | pinned`, una lista `dofs: ["uy", "uz"]` o i
sei flag `Dx..Rz`; `elements` accetta `ref: [x, y, z]` (vettore di
riferimento), `releases_i` / `releases_j` (liste di `ux..rz`), `shear`; le
altre liste (`concentrated_loads`, `thermal_loads`, `settlements`,
`prestress`) rispecchiano i loro fogli. Unita' SI, Y verticale per le travi
orizzontali, carichi gravitazionali `fy` negativi. `build_model` scrive il
workbook, lo valida e restituisce i rilievi, cosi' l'assistente puo'
correggere e riprovare.

## 3. Trasporti e sicurezza (MCP)

```bash
feagent mcp                                        # stdio: il client avvia il processo (client locali)
feagent mcp --transport http --port 8765           # streamable HTTP su http://127.0.0.1:8765/mcp
feagent mcp --transport http --root C:/modelli --api-key SEGRETO --allow-any-host --host 0.0.0.0
```

| Opzione | Effetto |
|---|---|
| `--root CARTELLA` | sandbox: i tool leggono e scrivono solo dentro la cartella (i percorsi relativi vengono risolti li') |
| `--api-key CHIAVE` (o `FEAGENT_API_KEY`) | le richieste HTTP devono portare `Authorization: Bearer CHIAVE` o `X-API-Key: CHIAVE` |
| `--allow-any-host` | disattiva la protezione DNS-rebinding (di default solo `localhost:porta` / `127.0.0.1:porta` sono accettati come `Host`); necessario dietro un tunnel o un reverse proxy |
| `--host`, `--port` | indirizzo di ascolto (default `127.0.0.1:8765`) |
| `--transport sse` | trasporto SSE legacy per i client piu' vecchi |

Per un client su un'altra macchina o in cloud (ChatGPT, connettori di Claude
web, agenti ospitati) esponi il server HTTP con un tunnel HTTPS, per esempio
`ngrok http 8765` o `cloudflared tunnel --url http://localhost:8765`, e dai
al client `https://<tunnel>/mcp`. Tieni attiva la sandbox, riservato l'URL e
spegni il server quando hai finito.

{: .warning }
I connettori MCP personalizzati di ChatGPT supportano "nessuna
autenticazione" o OAuth, non le chiavi API: per ChatGPT preferisci la strada
delle **Custom GPT Actions** sul server REST, che accetta una chiave Bearer
(sezione seguente). `feagent connect chatgpt` stampa entrambe le procedure.

## 4. REST e OpenAPI (`feagent serve`)

```bash
feagent serve                                    # http://127.0.0.1:8766, sandbox = cartella corrente
feagent serve --port 8766 --root C:/modelli --api-key SEGRETO --host 0.0.0.0
```

| Endpoint | Scopo |
|---|---|
| `GET /openapi.json` | documento OpenAPI 3.1 (un'operazione `POST /tools/{name}` per tool, schema di sicurezza quando c'e' la chiave); importalo in Custom GPT Actions, Open WebUI, n8n, Dify |
| `GET /tools` | definizioni dei tool (schema JSON) |
| `POST /tools/{name}` | esegue un tool con gli argomenti nel body JSON; `?format=png` restituisce l'immagine di `plot_model` |
| `GET /files/{path}` | scarica un file prodotto (risultati, figure, relazione) dalla sandbox |
| `GET /health`, `GET /` | stato e riepilogo |

```bash
curl -X POST http://127.0.0.1:8766/tools/check_model \
     -H "Authorization: Bearer SEGRETO" -H "Content-Type: application/json" \
     -d '{"path": "trave.xlsx", "solve": true}'

curl -X POST "http://127.0.0.1:8766/tools/plot_model?format=png" \
     -H "Authorization: Bearer SEGRETO" -H "Content-Type: application/json" \
     -d '{"path": "trave.xlsx", "what": "deformed", "cases": "G=1.35 Q=1.5"}' --output deformata.png
```

Il server non ha dipendenze oltre feagent, risponde ai pre-flight CORS
(`--cors ORIGINE`, `--no-cors`) e traduce gli errori in codici HTTP: `400`
argomenti errati, `401` chiave mancante, `403` percorso fuori dalla sandbox,
`404` file o tool inesistente, `501` pacchetto opzionale mancante.

## 5. Function calling senza server

Gli stessi tool si possono consegnare a qualsiasi API LLM come definizioni
di funzione ed eseguire nel proprio ciclo:

```python
from feagent import agent_api

tools = agent_api.tool_specs("openai")        # oppure "anthropic", "gemini", "json"
# ... invia `tools` insieme ai messaggi; quando il modello restituisce una chiamata:
result = agent_api.call("check_model", {"path": "trave.xlsx", "solve": True})
```

```python
# OpenAI Agents SDK su MCP (stdio)
from agents import Agent, Runner
from agents.mcp import MCPServerStdio

async with MCPServerStdio(params={"command": "python", "args": ["-m", "feagent", "mcp"]}) as feagent:
    agent = Agent(name="Strutturista", instructions="Usa i tool feagent.", mcp_servers=[feagent])
    result = await Runner.run(agent, "Valida trave.xlsx e risolvi la combinazione SLU")
```

```python
# LangChain (langchain-mcp-adapters)
from langchain_mcp_adapters.client import MultiServerMCPClient
client = MultiServerMCPClient({"feagent": {"command": "python", "args": ["-m", "feagent", "mcp"], "transport": "stdio"}})
tools = await client.get_tools()
```

`agent_api.set_root(cartella)` applica la stessa sandbox in-process;
`feagent tools --format openai|anthropic|gemini|openapi` stampa le
definizioni dal terminale.

## 6. Una sessione tipica

> **Tu:** Costruisci una trave appoggiata IPE300 di 6 m con 10 kN/m permanenti e 20 kN variabili in mezzeria, poi dammi l'inviluppo SLU.
>
> **Assistente:** legge `feagent://format`, chiama `build_model` con sei elementi, cerniera e carrello e due casi di carico, poi `check_model` (`ok: true`), `solve_combinations` con `SLU = 1.35 G + 1.5 Q` e `SLE = G + Q`, `plot_model` di `Mz` per la SLU, e risponde con il momento in mezzeria (SLU governante), la freccia in SLE, le reazioni e l'immagine.

## Risoluzione dei problemi

| Sintomo | Rimedio |
|---|---|
| `No module named 'mcp.server.fastmcp'` | e' installato il pacchetto `mcp` 2.x; feagent usa l'API 1.x: `pip install "mcp<2"` (l'extra `feagent[mcp]` lo blocca gia') |
| `mcp` non si installa | MCP richiede Python 3.10 o successivo; server REST e function calling funzionano anche con 3.9 |
| il client dice che il server non parte | esegui `python -m feagent mcp` in un terminale: l'errore compare li'; controlla che l'interprete nella configurazione sia quello con feagent installato |
| `421 Invalid Host header` in HTTP | la richiesta arriva da un tunnel o da un nome host diverso: avvia con `--allow-any-host` |
| `401` in HTTP | invia `Authorization: Bearer <chiave>` (o `X-API-Key`) uguale a `--api-key` |
| `percorso fuori dalla cartella consentita` | il percorso e' fuori da `--root`: usa percorsi dentro la sandbox |
| `plot_model` fallisce | installa `matplotlib` (`feagent[matplotlib]`); `create_report` richiede anche `python-docx` |
| `PermissionError` scrivendo un workbook | il file e' aperto in Excel |

Vedi anche [37 - Interfaccia a riga di comando](it-37-cli.html) per `mcp`,
`serve`, `tools` e `connect`, e [41 - Tutorial CLI](it-41-cli-excel-tutorial.html)
per il formato Excel su cui lavora l'assistente.
