---
layout: default
title: "38 - AI connector (MCP, REST, function calling)"
parent: English
nav_order: 38
---

# 38 - AI connector: use feagent from Claude, ChatGPT, Codex, Gemini, Copilot and any LLM

feagent ships a **connector** that lets AI assistants drive the solver:
validate a workbook, build a model from a description, run static, modal and
buckling analyses, solve every combination with envelopes, look at the
deformed shape, write the Word report. One registry of **17 tools**
(`feagent.agent_api`) feeds three channels, so every assistant talks to the
same feagent:

| Channel | Command | Who uses it |
|---|---|---|
| **MCP** (Model Context Protocol), stdio or HTTP | `feagent mcp` | Claude Desktop, Claude Code, ChatGPT connectors, OpenAI Codex CLI, Gemini CLI, GitHub Copilot (VS Code), Cursor, Windsurf, Cline, Continue, OpenAI Agents SDK, LangChain |
| **REST + OpenAPI** | `feagent serve` | Custom GPT Actions, Open WebUI, n8n, Dify, Power Automate, any HTTP client |
| **Function calling** (no server) | `feagent tools`, `feagent.agent_api` | your own Python code with the OpenAI, Anthropic or Gemini SDKs |

```bash
pip install "feagent[mcp]"            # adds the mcp SDK (Python 3.10+); REST and function calling need nothing extra
feagent connect                       # which clients can be configured, and where their config lives
feagent connect claude-desktop --write   # one command per client
```

{: .note }
Everything the AI can do is a normal feagent operation on files: read and
write workbooks, results, figures and reports. No tool executes arbitrary
code. With `--root` the servers are confined to one folder (sandbox).

## 1. Connect a client in one command

`feagent connect <client>` prints the configuration snippet for the client
and, with `--write`, merges it into the client's configuration file (a
`.bak` copy is kept). The registered command is the current Python
interpreter with `-m feagent mcp`, so it works even when the app cannot see
the `Scripts` folder; `--command feagent` registers the plain executable
instead.

| Client | Command | Notes |
|---|---|---|
| Claude Desktop | `feagent connect claude-desktop --write` | writes `claude_desktop_config.json`; restart the app, the tools appear in the tools menu |
| Claude Code | `feagent connect claude-code --write` (project `.mcp.json`) or `claude mcp add --scope user feagent -- python -m feagent mcp` | HTTP: `claude mcp add --transport http feagent http://127.0.0.1:8765/mcp` |
| ChatGPT | `feagent connect chatgpt` | prints the two routes: MCP connector (developer mode, HTTPS tunnel) or Custom GPT Actions on the REST server with an API key, see below |
| OpenAI Codex CLI | `feagent connect codex --write` | section `[mcp_servers.feagent]` of `~/.codex/config.toml` |
| Gemini CLI | `feagent connect gemini --write` (`--project` for `.gemini/settings.json`) | check with `/mcp` inside Gemini |
| VS Code / GitHub Copilot | `feagent connect vscode --project --write` (`.vscode/mcp.json`) or user `mcp.json` | Copilot Chat in Agent mode, tools icon |
| Cursor | `feagent connect cursor --write` (`--project` for `.cursor/mcp.json`) | Settings > MCP |
| Windsurf | `feagent connect windsurf --write` | `~/.codeium/windsurf/mcp_config.json` |
| Cline | `feagent connect cline --write` | `cline_mcp_settings.json` in the VS Code global storage |
| Continue | `feagent connect continue` | prints the YAML block for `~/.continue/config.yaml` |
| Open WebUI, n8n, Dify | `feagent connect openwebui` | REST server + `openapi.json` |
| Python SDKs | `feagent connect python` | snippets for OpenAI Agents SDK, function calling, LangChain |

Every MCP client can also point to a **running HTTP server** instead of
spawning a process: `feagent connect cursor --url http://127.0.0.1:8765/mcp
--api-key SECRET`. `feagent connect --json` returns the same data for
scripts.

## 2. What the assistant can do: the tools

| Tool | Purpose | Writes files |
|---|---|---|
| `version` | version, elements, analyses, sandbox root | |
| `excel_format` | sheets/columns of the Excel format, conventions, **JSON model spec** with a full example | |
| `list_examples`, `write_example` | catalog of the 10 bundled models; write one (optionally verified against closed-form formulas) | yes |
| `create_template` | Excel template (README + Combination sheets), `blank=True` for headers only | yes |
| `model_info` | geometry, materials, sections, loads per case, combinations | |
| `check_model` | validation with codes, sheet and Excel row; `solve=True` adds the trial analysis (mechanisms, equilibrium) | |
| `model_to_json` | the workbook as JSON records (inverse of `build_model`) | |
| `build_model` | **model from a JSON spec** (nodes, materials, sections, elements, supports, loads per case, combinations) written as a workbook and validated | yes |
| `solve_static` | one combination: max displacement, most displaced nodes, total reactions, optional results file | optional |
| `solve_combinations` | all named combinations (sheet or inline) with one factorization, one results file each, envelope workbook and **extremes of N, V, M with the governing combination** | yes |
| `modal_analysis`, `buckling_analysis` | frequencies / critical multipliers, optional HDF5 | optional |
| `read_results` | displacements, reactions, end forces, modes from a results file | |
| `plot_model` | PNG of model, loads, deformed shape, internal-force diagram or reactions; returned **as an image** to MCP clients | yes |
| `export_model` | OpenSees, SAP2000, MIDAS, Robot, Straus7 | yes |
| `create_report` | Word calculation report (one combination or all) | yes |

Load combinations are written as in the CLI: `"G=1.35 Q=1.5"` (coefficients)
or `"G Q"` (coefficient 1). Results are JSON; `plot_model` returns the PNG
both as a file and as `png_base64` (MCP clients receive it as an image).

The MCP server also publishes **resources** the assistant can read before
acting: `feagent://format` (the format and the JSON spec), `feagent://examples`
and `feagent://examples/{key}` (an example with its analytical checks and
sheets); and two **prompts**: `analyze_workbook(path)` (the recommended
check → info → solve → plot → summary sequence) and
`build_model_from_description(description, output_path)`.

### The JSON model spec (`build_model`)

The assistant can create a model without touching Excel:

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

Keys follow the Excel sheets ([11 - Excel I/O](en-11-excel-io.html)):
`supports` accept `type: fixed | pinned`, a list `dofs: ["uy", "uz"]` or the
six flags `Dx..Rz`; `elements` accept `ref: [x, y, z]` (reference vector),
`releases_i` / `releases_j` (lists of `ux..rz`), `shear`; the other lists
(`concentrated_loads`, `thermal_loads`, `settlements`, `prestress`) mirror
their sheets. Units are SI, Y is vertical for horizontal beams, gravity loads
are negative `fy`. `build_model` writes the workbook, validates it and
returns the findings, so the assistant can fix and retry.

## 3. Transports and security (MCP)

```bash
feagent mcp                                        # stdio: the client starts the process (local clients)
feagent mcp --transport http --port 8765           # streamable HTTP on http://127.0.0.1:8765/mcp
feagent mcp --transport http --root C:/models --api-key SECRET --allow-any-host --host 0.0.0.0
```

| Option | Effect |
|---|---|
| `--root FOLDER` | sandbox: tools read and write only inside the folder (relative paths are resolved there) |
| `--api-key KEY` (or `FEAGENT_API_KEY`) | HTTP requests must carry `Authorization: Bearer KEY` or `X-API-Key: KEY` |
| `--allow-any-host` | disables the DNS-rebinding protection (by default only `localhost:port` / `127.0.0.1:port` are accepted as `Host`); required behind a tunnel or reverse proxy |
| `--host`, `--port` | bind address (default `127.0.0.1:8765`) |
| `--transport sse` | legacy SSE transport for older clients |

For a client on another machine or in the cloud (ChatGPT, Claude web
connectors, hosted agents) expose the HTTP server through an HTTPS tunnel,
for example `ngrok http 8765` or `cloudflared tunnel --url
http://localhost:8765`, and give the client `https://<tunnel>/mcp`. Keep the
sandbox on, keep the URL private and stop the server when you are done.

{: .warning }
ChatGPT's custom MCP connectors support "no authentication" or OAuth, not API
keys: for ChatGPT prefer the **Custom GPT Actions** route on the REST server,
which accepts a Bearer key (next section). `feagent connect chatgpt` prints
both procedures.

## 4. REST and OpenAPI (`feagent serve`)

```bash
feagent serve                                    # http://127.0.0.1:8766, sandbox = current folder
feagent serve --port 8766 --root C:/models --api-key SECRET --host 0.0.0.0
```

| Endpoint | Purpose |
|---|---|
| `GET /openapi.json` | OpenAPI 3.1 document (one `POST /tools/{name}` operation per tool, security scheme when a key is set); import it in Custom GPT Actions, Open WebUI, n8n, Dify |
| `GET /tools` | tool definitions (JSON schema) |
| `POST /tools/{name}` | run a tool with the arguments in the JSON body; `?format=png` returns the image of `plot_model` |
| `GET /files/{path}` | download a produced file (results, figures, report) from the sandbox |
| `GET /health`, `GET /` | status and summary |

```bash
curl -X POST http://127.0.0.1:8766/tools/check_model \
     -H "Authorization: Bearer SECRET" -H "Content-Type: application/json" \
     -d '{"path": "beam.xlsx", "solve": true}'

curl -X POST "http://127.0.0.1:8766/tools/plot_model?format=png" \
     -H "Authorization: Bearer SECRET" -H "Content-Type: application/json" \
     -d '{"path": "beam.xlsx", "what": "deformed", "cases": "G=1.35 Q=1.5"}' --output deformed.png
```

The server has no dependencies beyond feagent, answers CORS pre-flight
requests (`--cors ORIGIN`, `--no-cors`) and maps errors to HTTP codes:
`400` bad arguments, `401` missing key, `403` path outside the sandbox,
`404` missing file or tool, `501` missing optional package.

## 5. Function calling without a server

The same tools can be handed to any LLM API as function definitions and
executed in your own loop:

```python
from feagent import agent_api

tools = agent_api.tool_specs("openai")        # or "anthropic", "gemini", "json"
# ... send `tools` with the messages; when the model returns a tool call:
result = agent_api.call("check_model", {"path": "beam.xlsx", "solve": True})
```

```python
# OpenAI Agents SDK over MCP (stdio)
from agents import Agent, Runner
from agents.mcp import MCPServerStdio

async with MCPServerStdio(params={"command": "python", "args": ["-m", "feagent", "mcp"]}) as feagent:
    agent = Agent(name="Structural engineer", instructions="Use the feagent tools.", mcp_servers=[feagent])
    result = await Runner.run(agent, "Validate beam.xlsx and solve the ULS combination")
```

```python
# LangChain (langchain-mcp-adapters)
from langchain_mcp_adapters.client import MultiServerMCPClient
client = MultiServerMCPClient({"feagent": {"command": "python", "args": ["-m", "feagent", "mcp"], "transport": "stdio"}})
tools = await client.get_tools()
```

`agent_api.set_root(folder)` applies the same sandbox in-process;
`feagent tools --format openai|anthropic|gemini|openapi` prints the
definitions from the terminal.

## 6. A typical session

> **You:** Build a 6 m simply supported IPE300 beam with 10 kN/m permanent load and a 20 kN live load at midspan, then give me the ULS envelope.
>
> **Assistant:** reads `feagent://format`, calls `build_model` with six elements, a pinned and a roller support and two load cases, then `check_model` (`ok: true`), `solve_combinations` with `SLU = 1.35 G + 1.5 Q` and `SLE = G + Q`, `plot_model` of `Mz` for SLU, and answers with the midspan moment (governing SLU), the deflection under SLE, the reactions and the picture.

## Troubleshooting

| Symptom | Fix |
|---|---|
| `No module named 'mcp.server.fastmcp'` | the `mcp` package 2.x is installed; feagent uses the 1.x API: `pip install "mcp<2"` (the `feagent[mcp]` extra already pins it) |
| `mcp` cannot be installed | MCP needs Python 3.10 or newer; the REST server and function calling work on 3.9 |
| the client says the server does not start | run `python -m feagent mcp` in a terminal: the error is printed there; check that the interpreter in the config is the one with feagent installed |
| `421 Invalid Host header` on HTTP | the request comes through a tunnel or a different host name: start with `--allow-any-host` |
| `401` on HTTP | send `Authorization: Bearer <key>` (or `X-API-Key`) matching `--api-key` |
| `percorso fuori dalla cartella consentita` | the path is outside `--root`: use paths inside the sandbox |
| `plot_model` fails | install `matplotlib` (`feagent[matplotlib]`); `create_report` needs `python-docx` too |
| `PermissionError` writing a workbook | the file is open in Excel |

See also [37 - Command-Line Interface](en-37-cli.html) for `mcp`, `serve`,
`tools` and `connect`, and [41 - CLI tutorial](en-41-cli-excel-tutorial.html)
for the Excel format the assistant works on.
