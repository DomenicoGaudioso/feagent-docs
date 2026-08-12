---
layout: default
title: "01 - Installazione"
parent: Italiano
nav_order: 1
---

# 01 - Installazione

## Requisiti

- Python ≥ 3.9
- numpy ≥ 1.24
- scipy ≥ 1.10

## Installazione

### Da sorgente (sviluppo)

```bash
git clone https://github.com/DomenicoGaudioso/beamfeapy.git
cd beamfeapy
pip install -e ".[all]"
```

### Solo le dipendenze base (numpy + scipy)

```bash
pip install -e .
```

## Extras

| Extra | Pacchetti | Descrizione |
|-------|-----------|-------------|
| `plot` | plotly, kaleido | Grafici interattivi Plotly |
| `excel` | pandas, openpyxl | Import/export Excel |
| `all` | plotly, kaleido, pandas, openpyxl | Tutti |
| `dev` | plotly, kaleido, pandas, openpyxl, pytest | Sviluppo + test |

Esempio:

```bash
pip install -e ".[all]"       # tutto
pip install -e ".[plot]"      # solo grafici
pip install -e ".[excel]"     # solo Excel
```

## Verifica dell'installazione

```python
import beamfeapy
print(beamfeapy.__version__)  # 0.4.1
```

## Esecuzione dei test

```bash
pip install -e ".[dev]"
python -m pytest tests -q
```

## Risoluzione problemi

### ImportError: plotly / pandas non trovati

Gli extras `plot` e `excel` non sono installati. Esegui:

```bash
pip install -e ".[all]"
```

### ValueError: ref_vector parallelo all'asse della trave

Per gli elementi verticali (direzione Z), non usare `ref_vector=(0,0,1)` che è parallelo all'asse. Usa il default (che sceglie automaticamente Y) oppure `ref_vector=(1,0,0)` per un'altra orientazione.