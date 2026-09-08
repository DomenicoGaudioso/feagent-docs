---
layout: default
title: "38 - MCP Server (AI agents)"
parent: English
nav_order: 38
---

# 38 - MCP Server (AI agents)

feagent ships an **MCP server** so AI coding agents such as **Claude Code** and
**Codex** can drive the FEM solver directly: load an Excel model, run static,
modal or buckling analyses, and read back structured results — all through the
[Model Context Protocol](https://modelcontextprotocol.io).

```bash
pip install "feagent[mcp]"   # adds the 'mcp' SDK
feagent mcp                  # start the MCP server on stdio
```

The server speaks MCP over **stdio**, the transport both Claude Code and Codex
use for local tools.

## Exposed tools

| Tool | Purpose |
|------|---------|
| `version` | package version and supported elements/analyses |
| `model_info` | summary of an Excel model (nodes, beams, shells, loads, cases) |
| `solve_static` | linear static analysis; optional Excel/HDF5 output |
| `modal_analysis` | natural frequencies and participating masses |
| `buckling_analysis` | linear buckling critical multipliers |
| `create_template` | generate an empty Excel input template |
| `check_model` | validate a workbook (references, supports, loads, combinations), optional trial analysis; same findings as `feagent check` |
| `list_examples` | catalog of the bundled example models (cantilever, portal frame, grillage, prestress, ...) |
| `write_example` | write an example workbook, optionally verifying it against its closed-form checks |
| `export_model` | export the model to an external solver (e.g. OpenSees) |

Models are passed as paths to `.xlsx` files in the feagent format. Load
combinations are strings like `"G=1.35 Q=1.5"` (with coefficients) or `"G Q"`
(coefficient 1). Every tool returns JSON.

## Register in Claude Code

```bash
claude mcp add feagent -- feagent mcp
```

Or add it to a project `.mcp.json`:

```json
{
  "mcpServers": {
    "feagent": { "command": "feagent", "args": ["mcp"] }
  }
}
```

## Register in Codex

Add the server to `~/.codex/config.toml`:

```toml
[mcp_servers.feagent]
command = "feagent"
args = ["mcp"]
```

If `feagent` is not on the agent's `PATH`, use the Python module form instead:

```toml
[mcp_servers.feagent]
command = "python"
args = ["-m", "feagent", "mcp"]
```

## Example agent workflow

1. `create_template` → get a blank `model.xlsx`, fill it in (or point at an
   existing model).
2. `model_info` → confirm nodes, elements and load cases.
3. `solve_static` (or `modal_analysis` / `buckling_analysis`) → run the analysis
   and read the JSON results; pass `output` to also save an `.xlsx`/`.h5` file.

> Note: the MCP stdio channel uses **stdout** for the JSON-RPC protocol, so the
> CLI banner is suppressed for `feagent mcp` and the tools never write to stdout.
