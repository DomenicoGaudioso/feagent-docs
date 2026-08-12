---
layout: default
title: "32 - Ponte Bitrave in Sezione Mista"
parent: "14 - Esempi d'Uso"
grand_parent: Italiano
nav_order: 7
---

# 32 - Ponte bitrave a tre campate in sezione mista

Questo esempio sviluppa un **ponte stradale continuo a tre campate**
(50 + 60 + 50 m) con impalcato a graticcio a **due travi** principali in
acciaio e soletta collaborante in calcestruzzo (sezione mista). È il compagno
dell'[esempio 30 - ponte a graticcio](it-30-ponte-graticcio-misto.html), qui con
due sole travi e con **conci strutturali variabili** lungo l'impalcato.

## Schema del modello

- ponte continuo a tre campate: 50 + 60 + 50 m
- impalcato a graticcio: 2 travi principali (interasse 7.0 m) + traversi
- soletta in calcestruzzo collaborante (sezione mista)
- vincoli verticali su entrambe le travi a ogni linea d'appoggio; appoggio fisso
  (longitudinale + laterale) a un solo angolo, gli altri liberi di dilatare

![Anteprima del modello bitrave](images/ex32_ponte_bitrave_report_preview.png)

## Conci strutturali (sezioni che compongono l'impalcato)

L'impalcato non ha sezione costante: come nei ponti reali si distinguono più
conci, definiti in
[`scripts/_bridge_sections.py`](https://github.com/DomenicoGaudioso/beamfeapy/blob/main/scripts/_bridge_sections.py)
e calcolati per omogeneizzazione (modulo efficace del cls, $n_L$):

| Concio | Posizione | $A$ [m²] | $I_{\text{vert}}$ [m⁴] | Stato |
|--------|-----------|---------:|-----------------------:|-------|
| Campata di riva | campate laterali | 0.153 | 0.141 | non fessurata |
| Campata centrale | campata centrale | 0.175 | 0.201 | non fessurata |
| Appoggio interno | sugli appoggi interni | 0.177 | 0.208 | **fessurata** |

I conci sugli appoggi interni sono trattati come **fessurati** (cls teso
trascurato, sezione acciaio + armatura), con flange più spesse.

## Casi di carico

| Caso | Descrizione |
|------|-------------|
| DEAD | Peso proprio nominale (acciaio + soletta) |
| SDL | Permanenti portati (pavimentazione, barriere) |
| LANE_EQ | Carico di corsia statico equivalente, eccentrico su una trave |
| THERM | Variazione termica uniforme + gradiente verticale |
| SHR | Ritiro come deformazione imposta, solo sui conci non fessurati |

> Il ritiro segue la trattazione rigorosa di
> [31 - Ritiro nelle sezioni composte](it-31-ritiro-sezioni-composte.html):
> effetto primario isostatico ($N$, $N\cdot e$) ed effetto secondario
> iperstatico, con applicazione ai soli conci non fessurati e somma delle
> tensioni in fase di verifica.

## Combinazioni analizzate

- Permanenti: `DEAD + SDL`
- Esercizio: `DEAD + SDL + LANE_EQ`
- Termico e ritiro: `THERM + SHR`
- Rara: tutti i casi

Le sollecitazioni sono in unità tecniche (momenti in kN·m, tagli/sforzo normale
in kN) e la deformata in mm; nei diagrammi 3D l'asse fuori-piano riporta il
valore della sollecitazione.

![Momento flettente Mz sul ponte bitrave](images/ex32_ponte_bitrave_Mz_preview.png)

## Report Word

[Scarica il report Word del ponte bitrave](assets/ex32_ponte_bitrave_report.docx)

Per rigenerare il documento:

```bash
python scripts/generate_twin_girder_bridge_word_report.py
```

Nucleo del flusso:

```python
from scripts.generate_twin_girder_bridge_word_report import (
    build_twin_girder_model,
    solve_cases,
)
from beamfeapy.reporting import create_word_report

model, groups = build_twin_girder_model()
results, combinations = solve_cases(model)

report = create_word_report(
    model,
    results,
    options={
        "title": "Ponte bitrave a tre campate in sezione mista - Report FEM",
        "load_cases": ["DEAD", "SDL", "LANE_EQ", "THERM", "SHR"],
        "combinations": combinations,
        "force_components": ["Mz", "Vy", "N"],
    },
)
```
