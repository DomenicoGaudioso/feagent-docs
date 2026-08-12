---
layout: default
title: "13 - Convenzioni"
parent: Italiano
nav_order: 13
---

# 13 - Convenzioni

## Gradi di libertà nodali

Ogni nodo ha 6 GdL nell'ordine: `[ux, uy, uz, rx, ry, rz]`

- `ux`, `uy`, `uz`: traslazioni lungo gli assi globali X, Y, Z
- `rx`, `ry`, `rz`: rotazioni attorno agli assi globali X, Y, Z

## Assi locali dell'elemento

Per un elemento che va dal nodo i al nodo j (convenzione SAP2000 / Przemieniecki):

- **x locale**: dal nodo i al nodo j (asse della trave)
- **y locale**: `ref_vector` proiettato ⊥ a x — `e_y = normalize(ref − (ref·e_x)·e_x)`
- **z locale**: `z = x × y` (prodotto vettoriale, sistema destrorso)

`ref_vector` definisce direttamente la **direzione di y locale**. Vedi
[08 - Orientazione Sezione](it-08-section-orientation.html) per dettagli ed esempi.

**Convenzioni momenti inerziali**:
- `Iz` → flessione nel piano x-y (forze `fy`, momento `Mz`)
- `Iy` → flessione nel piano x-z (forze `fz`, momento `My`)

Per travi orizzontali con `ref_vector` default `(0,1,0)`, `local_y = Y globale`,
quindi `Iz` governa la flessione gravitazionale (**asse forte**).

**Convenzione segni rotazioni** (importante per le funzioni di forma):
- `theta_z = +du_y/dx`
- `theta_y = -du_z/dx`

## Segno delle azioni interne

- **Sforzo normale N**: positivo in trazione
- **Taglio Vy, Vz**: positivo nella direzione positiva dell'asse locale corrispondente
- **Momento torcente T**: positivo antiorario (vista da +x)
- **Momenti flettenti My, Mz**: convenzione europea (momento negativo all'estradosso)

## Carichi distribuiti

- Componenti `fx`, `fy`, `fz`: forze per unità di lunghezza
- Componenti `mx`, `my`, `mz`: coppie per unità di lunghezza
- Positive nelle direzioni positive degli assi locali/globali

## Unità

Il sistema è **incoerente**: l'utente sceglie le unità purché coerenti. Esempio con SI:

| Grandezza | Unità |
|-----------|-------|
| Lunghezza | m |
| Forza | N |
| Pressione | Pa |
| Temperatura | °C |
| Momento | Nm |

## Default dell'orientazione

Se `ref_vector` non è specificato:
- Elementi **non verticali** (|e_x·Y| < 0.999): `ref = (0,1,0)` → y locale = Y globale
- Elementi **verticali** (|e_x·Y| ≥ 0.999): `ref = (1,0,0)` → y locale = X globale
  (evita la singolarità di `ref` parallelo all'asse della trave)