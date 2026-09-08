---
layout: default
title: "38 - Server MCP (agenti AI)"
parent: Italiano
nav_order: 38
---

# 38 - Server MCP (agenti AI)

feagent include un **server MCP** che permette ad agenti di programmazione AI
come **Claude Code** e **Codex** di pilotare direttamente il solutore FEM:
caricare un modello Excel, eseguire analisi statica, modale o di buckling e
leggere i risultati strutturati — tramite il
[Model Context Protocol](https://modelcontextprotocol.io).

```bash
pip install "feagent[mcp]"   # aggiunge l'SDK 'mcp'
feagent mcp                  # avvia il server MCP su stdio
```

Il server comunica via **stdio**, il trasporto usato sia da Claude Code sia da
Codex per gli strumenti locali.

## Tool esposti

| Tool | Scopo |
|------|-------|
| `version` | versione del pacchetto ed elementi/analisi supportati |
| `model_info` | riepilogo di un modello Excel (nodi, travi, gusci, carichi, casi) |
| `solve_static` | analisi statica lineare; salvataggio opzionale Excel/HDF5 |
| `modal_analysis` | frequenze proprie e masse partecipanti |
| `buckling_analysis` | moltiplicatori critici di buckling lineare |
| `create_template` | genera un template Excel di input vuoto |
| `check_model` | valida un workbook (riferimenti, vincoli, carichi, combinazioni), con analisi di prova opzionale; stessi rilievi di `feagent check` |
| `list_examples` | catalogo dei modelli d'esempio distribuiti (mensola, portale, graticcio, precompressione, ...) |
| `write_example` | scrive un workbook d'esempio, con verifica opzionale contro le sue formule chiuse |
| `export_model` | esporta il modello verso un solutore esterno (es. OpenSees) |

I modelli si passano come percorsi a file `.xlsx` nel formato feagent. Le
combinazioni di carico sono stringhe tipo `"G=1.35 Q=1.5"` (con coefficienti) o
`"G Q"` (coefficiente 1). Ogni tool restituisce JSON.

## Registrazione in Claude Code

```bash
claude mcp add feagent -- feagent mcp
```

Oppure nel file di progetto `.mcp.json`:

```json
{
  "mcpServers": {
    "feagent": { "command": "feagent", "args": ["mcp"] }
  }
}
```

## Registrazione in Codex

Aggiungi il server a `~/.codex/config.toml`:

```toml
[mcp_servers.feagent]
command = "feagent"
args = ["mcp"]
```

Se `feagent` non è nel `PATH` dell'agente, usa la forma a modulo Python:

```toml
[mcp_servers.feagent]
command = "python"
args = ["-m", "feagent", "mcp"]
```

## Flusso d'uso tipico

1. `create_template` → ottieni un `model.xlsx` vuoto da compilare (oppure punta a
   un modello esistente).
2. `model_info` → verifica nodi, elementi e casi di carico.
3. `solve_static` (o `modal_analysis` / `buckling_analysis`) → esegui l'analisi e
   leggi i risultati JSON; con `output` salvi anche un file `.xlsx`/`.h5`.

> Nota: il canale MCP stdio usa **stdout** per il protocollo JSON-RPC, perciò il
> banner della CLI è soppresso per `feagent mcp` e i tool non scrivono mai su stdout.
