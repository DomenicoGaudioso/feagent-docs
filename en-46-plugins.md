---
layout: default
title: "46 - Plugins and tools"
parent: English
nav_order: 46
---

# 46 - Plugins and tools

**Plugins** add specialised tools to feagent (model generators, dedicated
analyses, digital twin) that run on **feagent's own solver**: no plugin ships its
own solver and none needs OpenSees.

## Available tools

| Id | Tool | What it does |
|---|---|---|
| `bridge` | Bridge FEM model | builds the analytical model of composite (box, twin girder, grillage) and reinforced concrete decks from an Excel bridge description or from parameters |
| `train` | Train on bridge | rail convoy crossing a beam, speed sweep, accelerations, critical speed, vehicle-structure interaction |
| `frames` | Frames | plane frame, space frame with NTC 2018 actions, and frame with RC core |
| `trussgirder` | Trusses | parametric generation (Warren, Howe, Pratt, ...), static, modal and pushover analysis |
| `digitaltwin` | Digital twin | FEM vs measured frequencies, Sobol sensitivity, model updating |

## Using them

**GUI.** *Plugin* menu: pick tool and action, fill in the form (built from the
parameters the plugin declares) and run. A generated model opens in the window
with its load cases and combinations; tables and charts appear in a results dialog.

**Command line.**

```bash
feagent plugin list
feagent plugin info frames
feagent plugin run frames telaio_piano --param n_piani=3 -o frame.xlsx
```

**Library.**

```python
from feagent import plugins
res = plugins.get("frames").run("telaio_piano", n_piani=3)
res.model.solve()
```

**AI assistants.** The `plugin_list`, `plugin_info` and `plugin_run` tools (MCP,
REST, function calling) describe and run any plugin; `gui_plugin_run` opens the
produced model in the running GUI.

## Writing a plugin

A plugin is a module with a `PLUGIN` variable (see the Italian page for a full
example): an `Plugin` with `Action`s, each with typed `Param`s, returning a
`Result` (`model`, `combinations`, `tables`, `charts`, `files`, `data`,
`warnings`). Plugins are discovered among the bundled ones, the packages with the
`feagent.plugins` entry point and the `.py` files in `~/.feagent/plugins` (or
`FEAGENT_PLUGINS_DIR`). A plugin that fails to import never breaks feagent: it
stays listed with the reason.
