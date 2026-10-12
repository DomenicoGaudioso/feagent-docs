---
layout: default
title: "56 - What's new in version 1.1"
parent: English
nav_order: 56
---

# 56 - What's new in version 1.1

This page collects what changed from 0.9 to 1.1 and, for each new feature, how
to use it and where to read the details. 1.1 is the first version with an
executable after 0.9: it also includes the work of 1.0 (the plugin system).

## How to upgrade

The [v1.1.0](https://github.com/DomenicoGaudioso/feagent/releases/tag/v1.1.0)
release contains three files:

| File | What it is for |
|---|---|
| `feagent-gui-windows.exe` | the desktop interface for Windows, without Python; protected executable, licence on one PC |
| `feagent-1.1.0-py3-none-any.whl` | the Python package, to install with pip |
| `feagent-1.1.0.tar.gz` | the sources of the version |

```bash
pip install "feagent[all] @ file:///C:/Download/feagent-1.1.0-py3-none-any.whl"
```

With a clone, `git pull` and `pip install -e ".[all]"` are enough. Parallel
computing also uses `threadpoolctl`, included in the `gui`, `parallel` and
`all` extras. Full installation notes in [01 - Installation](en-01-installation.html).

## Topology optimisation

Removes material where it is not needed, on shell or solid meshes, with SIMP
(continuous densities) and BESO (solid or void elements); the result gives the
reduced model, re-analysed like any other.

```python
from feagent.topology import ottimizza

r = ottimizza(model, frazione=0.3, metodo="beso", casi={"Q": 1.0})
m2 = r.modello_ridotto()
```

In the GUI: **Analisi > Ottimizzazione topologica (SIMP/BESO)…**, then
**Risultati > Storia dell'ottimizzazione** and **Risultati > Apri il modello
ridotto**.

In 1.1 BESO no longer detaches the load from the supports (before, with a point
load and low fractions, the compliance could end at 10⁸-10⁹), and the SIMP
reduced model of a thin plate is built in a few seconds without spurious
mechanisms. Worked example, choice of the method, parameters and messages in
[55 - Topology optimization](en-55-topology-optimization.html).

![BESO density](images/gui_topology_beso.png)
*Figure 1 — Cantilever plate optimised with BESO in the GUI: solid (orange) or void (purple) shells.*

## Stresses in beams and shells, composite sections by phases

Beams now have the normal stresses in the fibres of the section and shells the
stresses on their faces. Composite sections are described by parts
(`SectionPart` sheet: steel, slab, rebars) and by phases (`StressPhase` sheet:
casting, permanent loads, shrinkage, traffic, each with its own ratio *n* and
load cases), and stresses are added phase by phase.

In the GUI: **Risultati > σ max travi / σ min travi**, **Tensione in una
fibra**, the coloured extruded view (key E) and **Tensioni nella sezione…** with
the σ(y) diagram. See [47 - Stresses](en-47-stresses.html).

## Parallel and multithreaded computing

Independent analyses (load cases, combinations, moving loads, dynamic analyses)
can be spread over several processes, and the linear algebra over several
threads. It is off by default; the number of processes and threads is taken from
the PC.

```python
import feagent.parallel as par

par.configure(parallel=True)                 # automatic number of processes
results = m.solve_many(combos)
```

From the command line `--parallel`, `--workers N`, `--multithread`,
`--threads N`; in the GUI **Strumenti > Opzioni > Calcolo**. Timings and advice
in [29 - Performance](en-29-performance.html).

## Plugins

Tools built on the feagent engine, available from the **Plugin** menu of the
GUI, from `feagent plugin` and from the AI tools:

| Plugin | What it does | Page |
|---|---|---|
| Bridge | grillage model of composite and RC decks | [Bridge plugin](en-plugin-bridge.html) |
| Train | a train crossing a bridge | [Train plugin](en-plugin-train.html) |
| Frames | plane, spatial and core frames | [Frames plugin](en-plugin-frames.html) |
| TrussGirder | truss girders: static, modal, pushover | [TrussGirder plugin](en-plugin-trussgirder.html) |
| DigitalTwin | digital twin: comparison with dynamic identification, sensitivity, calibration | [DigitalTwin plugin](en-plugin-digitaltwin.html) |
| MasoBridge | masonry arch bridges with solid elements: static, thrust line, nonlinear capacity, earthquake | [MasoBridge plugin](en-plugin-masobridge.html) |
| GerberNL | Gerber saddles with the actual reinforcement, nonlinear analysis up to failure | [46 - Plugins](en-46-plugins.html) |

How to write a plugin: [46 - Plugins](en-46-plugins.html).

## Nonlinear solids

Hexahedra-only models have a nonlinear solver (Newton with load or displacement
control) with the Drucker-Prager, masonry Rankine-and-cone and IMPL-EX
tension/compression damage laws, reinforcing bars embedded in the hexahedra,
rigid plates on a pin and time-history. It is used today through the MasoBridge
and GerberNL plugins: see [43 - Solid elements](en-43-solid-elements.html) and
the [MasoBridge plugin](en-plugin-masobridge.html).

## Executable, licence and usage reporting

The Windows executable is protected (the code is compiled) and is activated
with a civilBox account on one PC per account; the sign-in screen links to the
registration page on civilbox.xyz. The executable records every start and every
analysis (program, version, system, never the model data), even without an
account; the package installed with pip sends nothing. See
[42 - Desktop GUI, Usage reporting](en-42-desktop-gui.html#usage-reporting).

## Other fixes in 1.1

* Density map with a uniform colour per shell: averaging at the nodes made even
  the BESO result look grey.
* The reduced model opened from the GUI keeps the vertical axis of the starting
  model and no longer carries the nodes left without shells (before, they
  showed up as hundreds of supports).
* Mechanism search of the reduced model on the scaled stiffness: before, with
  thin plates, it could take minutes and return a singular model.
