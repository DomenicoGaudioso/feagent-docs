---
layout: default
title: "49 - Plugin Frames"
parent: Italiano
nav_order: 49
---

# 49 - Plugin Frames

Il plugin `frames` porta in feagent i tre motori del programma Telai: **telaio piano 2D**,
**telaio spaziale 3D di sole aste** e **telaio 3D con nucleo in c.a.**, con l'analisi
NTC 2018 (casi tipizzati, combinazioni, sisma statico equivalente, inviluppi), le
verifiche degli elementi in legno e la relazione Word. Il calcolo è **solo di feagent**:
nessuna dipendenza da OpenSees né dai repository d'origine.

## Come è calcolato

* **Telaio piano.** Modello feagent nel piano XY con `uz`, `rx`, `ry` bloccati su ogni
  nodo: restano i 3 gradi di libertà del telaio piano (`ux`, `uy`, `rz`). Le aste sono
  travi di Eulero Bernoulli con asse locale `y` ruotato di +90° rispetto all'asse; i
  carichi distribuiti locali `qx`, `qy` variano linearmente fra l'estremo `i` e `j` e sono
  integrati in forma esatta.
* **Telaio spaziale.** Travi di Eulero Bernoulli a 6 gradi di libertà, con gli assi locali
  della convenzione storica del programma: `x` lungo l'asta, `y = z_globale × x` (per i
  pilastri si usa `y_globale` come riferimento), `z = x × y`. Le forze d'estremità locali
  coincidono con quelle del solutore d'origine (stessa convenzione di segno).
* **Telaio con nucleo.** Come il precedente, con le pareti modellate con **ShellQ4**
  (membrana e flessione di Mindlin, 6 gradi di libertà per nodo), che entrano nella
  rigidezza di ogni caso. Il peso proprio delle pareti si somma al primo caso G1
  (colonna `peso` di `shell_sections`, in kN/m³).
* **Analisi NTC 2018.** Ogni caso elementare è risolto una volta sul modello feagent
  (un solo modello porta tutti i casi). Poi, come nel programma d'origine: peso sismico
  `G1 + G2 + Σψ2·Qk`, periodo fondamentale da analisi modale di feagent (`Model.modal`,
  masse orizzontali `W/g`, i gradi di libertà senza massa sono condensati), spettro SLV
  con `q` e spettro elastico SLD, forze statiche equivalenti con eccentricità accidentale
  del 5 per cento, combinazioni del paragrafo 2.5.3, inviluppi per gruppo di elementi,
  reazioni, drift SLD, `d_E` allo SLV e controlli di equilibrio. I casi sismici (`SLV+Xa`,
  `SLV-Xa`, ...) sono registrati come casi di carico del modello e le combinazioni sono
  restituite in `Result.combinations`: l'interfaccia le mostra e le risolve direttamente.

### Differenze rispetto al programma d'origine

| Aspetto | Programma d'origine | Plugin |
|---|---|---|
| Solutore | OpenSees (`elasticBeamColumn`, `ShellMITC4`) | feagent (`add_beam`, `ShellQ4`) |
| Carico distribuito trapezio, 3D | 10 forze concentrate per asta | integrazione esatta |
| Carico distribuito trapezio, 2D | media dei due estremi | integrazione esatta (uguale per i carichi uniformi) |
| Pareti | ShellMITC4 | ShellQ4 Mindlin: vedi lo scarto qui sotto |
| Pagine dell'interfaccia (Streamlit) | sì | no: l'interfaccia è quella di feagent, costruita dai parametri delle azioni |

**Scarto del guscio.** Misurato con il solutore d'origine sugli stessi modelli:

| Modello | Grandezza | ShellMITC4 | ShellQ4 | Scarto |
|---|---|---|---|---|
| Parete a mensola 4 × 6 m, 2 × 3 elementi | spostamento in testa (m) | 2,365e-4 | 2,442e-4 | +3,3 % |
| Parete a mensola 4 × 6 m, 4 × 6 elementi | spostamento in testa (m) | 2,675e-4 | 2,705e-4 | +1,1 % |
| Parete a mensola 4 × 6 m, 8 × 12 elementi | spostamento in testa (m) | 2,780e-4 | 2,790e-4 | +0,4 % |
| Telaio con nucleo di esempio | periodo T1 (s) | 0,1991 | 0,2009 | +0,9 % |
| Telaio con nucleo di esempio | drift SLD massimo (rapporto al limite) | 0,1290 | 0,1305 | +1,2 % |
| Telaio con nucleo di esempio | spostamento allo SLV (mm) | 22,33 | 22,50 | +0,8 % |

Entrambi gli elementi convergono dal basso verso la soluzione a trave tozza
(flessione e taglio di Timoshenko, 2,87e-4 m). Sui telai di sole aste i risultati
coincidono con il solutore d'origine fino alla precisione numerica: periodi, drift,
reazioni e sollecitazioni scartano meno di 1e-9 in valore relativo.

## Unità di misura

Unità coerenti, di norma **kN, m**: `E` in kN/m², carichi di superficie in kN/m²,
masse in t (`W/g` con `g` in m/s²). Nessuna conversione è applicata.

## Azioni

| Azione | Cosa fa | Uscite principali |
|---|---|---|
| `telaio_piano` | genera un telaio multipiano e multicampata (o usa `modello`) e risolve un caso | modello, sintesi, reazioni, spostamenti, sollecitazioni agli estremi e lungo le aste, equilibrio |
| `telaio_spaziale` | telaio regolare di aste (o `modello`), analisi NTC 2018 | modello con tutti i casi, `combinations`, sintesi, equilibrio, sisma, modi, inviluppi, reazioni, drift, spettri |
| `telaio_nucleo` | come sopra con pareti di nucleo in c.a. | come sopra, più `n_pareti` |
| `verifica_legno` | trave in legno su due appoggi con sbalzi (NTC 4.4) | sintesi, verifiche SLU e SLE, combinazioni, inviluppo del momento |
| `relazione_telaio_piano` | analisi più relazione Word | file `.docx` |
| `relazione_telaio_spaziale` | analisi NTC più relazione Word | file `.docx` |
| `relazione_telaio_nucleo` | analisi NTC con nucleo più relazione Word | file `.docx` |
| `da_json` | accetta il JSON del `compute` del programma d'origine | come l'analisi richiesta |

### Parametri del telaio piano

| Parametro | Tipo | Unità | Default | Note |
|---|---|---|---|---|
| `n_campate`, `n_piani` | intero | | 2, 3 | |
| `larghezza_campata`, `altezza_piano` | numero | m | 5, 3,2 | |
| `E`, `A`, `I` | numero | kN/m², m², m⁴ | 3e7, 0,12, 0,0036 | stessa sezione per pilastri e travi |
| `q_travi` | numero | kN/m | −18 | carico uniforme locale `y`, negativo verso il basso |
| `F_orizzontale` | numero | kN | 0 | per piano, sul nodo di destra |
| `caso` | intero | | 1 | caso di carico da risolvere |
| `modello` | JSON | | | fogli `nodes`, `elements`, `properties`, `load_cases`, `restraints`, `node_loads`, `dist_loads` |

### Parametri del telaio spaziale e del telaio con nucleo

| Gruppo | Parametri |
|---|---|
| Geometria | `campate_x`, `campate_y`, `altezze` (elenchi separati da virgola, in m) |
| Sezioni | `pilastro_b`, `pilastro_h`, `trave_b`, `trave_h` (m), `E` (kN/m²), `peso_specifico` (kN/m³) |
| Carichi | `g1_solaio`, `g2`, `q`, `neve`, `vento` (kN/m²), `categoria_q` (A...H), `orditura` (X o Y) |
| Nucleo | `posizione_nucleo`, `spessore_parete`, `E_parete`, `peso_parete` |
| Sisma | `sisma`, `suolo`, `topo`, `q_comportamento`, `ag_SLV`, `F0_SLV`, `TCs_SLV`, `ag_SLD`, `F0_SLD`, `TCs_SLD`, `metodo_T1`, `rigidezza_fessurata`, `eccentricita`, `coeff_drift`, `n_modi` |
| Combinazioni | `G2_compiutamente_definiti` |
| Avanzate | `parametri` (JSON: qualsiasi campo di `ParametriNTC`), `modello` (JSON: i fogli), `parametri_legno` (JSON) |

Il generatore costruisce un telaio regolare in c.a.: i carichi di superficie dei solai
diventano carichi lineari sulle travi dell'`orditura` scelta (larghezza d'influenza di
mezzo interasse per lato), il caso `Q` agisce su tutti gli impalcati tranne l'ultimo, la
`neve` sulla copertura, il `vento` come forze nodali sui lati sopravento nelle due
direzioni. Con `modello` si analizza invece un telaio qualunque, descritto dai fogli del
programma d'origine (`nodes`, `elements`, `properties`, `load_cases` con le colonne `tipo`,
`categoria`, `gruppo`, `restraints`, `node_loads`, `dist_loads`; per il nucleo
`beam_elements`, `beam_properties`, `shell_elements`, `shell_sections`, `beam_dist_loads`).
Se `properties` contiene la colonna `materiale` con una classe EN 338 o EN 14080, gli
elementi sono verificati anche come legno (NTC 4.4) e le tabelle `Legno ...` si aggiungono
all'esito.

### Il modello restituito

`Result.model` è il modello feagent con tutti i casi di carico (elementari e sismici) e
`Result.combinations` le combinazioni SLU, SLE rara, frequente e quasi permanente, SLV e
SLD, come dizionari `{caso: coefficiente}`. L'interfaccia le accoppia al modello; da
libreria basta `result.model.solve(cases=result.combinations["SLU_001"])`.

## Esempi

```python
from feagent import plugins
p = plugins.get("frames")

# telaio piano 3 x 4, forze orizzontali ai piani
r = p.run("telaio_piano", n_campate=3, n_piani=4, F_orizzontale=25)
print(r.data["M_max_abs"], r.data["reazione_risultante_Rx"])

# telaio spaziale con sisma SLV e SLD
r = p.run("telaio_spaziale", campate_x="5, 5", campate_y="4, 4", altezze="3.2, 3.2, 3.2",
          neve=1.2, vento=1.0, ag_SLV=0.15)
print(r.message)                         # periodi e numero di combinazioni
sintesi = r.tables[0]                    # tabella di sintesi dell'analisi
res = r.model.solve(cases=r.combinations["SLV_001"])

# telaio con nucleo, relazione Word
p.run("relazione_telaio_nucleo", campate_x="4, 4, 4", campate_y="4, 4, 4", altezze="3.5, 3.2, 3.2",
      file="relazione_nucleo.docx")

# trave in legno
r = p.run("verifica_legno", classe="C24", b=0.10, h=0.20, L=4.0, neve=1.2, g2=1.0)
print(r.data["eta_SLU"], r.data["eta_SLE"])
```

Da riga di comando:

```bash
feagent plugin run frames telaio_piano --param n_piani=4 --param F_orizzontale=25
feagent plugin run frames telaio_spaziale --param campate_x="5, 5" --param altezze="3.2, 3.2"
```

## Compatibilità: l'azione `da_json`

Chi usa già l'API del programma d'origine può passare lo stesso JSON. Due forme:

```python
# (1) analisi statica di un caso di carico: il campo "telaio" sceglie il motore
p.run("da_json", payload={"telaio": "piano2d", "load_case_id": 1, "nodes": [...], "elements": [...],
                          "properties": [...], "load_cases": [...], "restraints": [...], "node_loads": [...]})
# (2) funzione specifica
p.run("da_json", payload={"funzione": "analisi_ntc18", "nodes": [...], ..., "parametri": {"sisma": True}})
```

Valori di `telaio`: `piano2d`, `spaziale3d`, `nucleo` (sono accettati anche `Telaio2D`, `telaio3D`,
`TelaioNucleo`). Valori di `funzione`: `analisi_ntc18`, `analisi_ntc18_nucleo`, `spettro_ntc18`,
`combinazioni_ntc18`, `verifica_trave_legno`, `verifiche_legno_telaio`. Se il caso richiesto non ha
righe in `restraints`, l'analisi statica usa i vincoli del primo caso che le ha e lo segnala fra gli
avvisi. I campi `trapezoid_segments`, `geom_transf`, `shell_element_type` e `directory_output` sono
ignorati: il carico trapezio è esatto e i file si scrivono con le azioni di relazione.

## Cosa è portato e cosa no

Portato com'era: spettro, combinazioni, sisma statico equivalente, inviluppi (`ntc18`), verifiche
del legno e schede di materiale (`legno`), generatore dei telai, esempi, lettura e scrittura XLSX,
relazione Word (riscritta nello stile aziendale e senza dipendere dal solutore). Sostituito solo il
livello che chiamava OpenSees. Non portato: le pagine Streamlit (`app.py`, `ui_ntc.py`, `ui_legno.py`),
perché l'interfaccia è quella di feagent, il server HTTP e l'envelope dell'API, il grafico plotly del
telaio piano (nella relazione c'è una figura equivalente), le masse nodali del foglio `masses`
(nel programma d'origine non influivano sull'analisi statica). Non è prevista l'opzione di
impalcato rigido: come nel programma d'origine ogni nodo ha le sue masse e i suoi spostamenti.

## Verifica

I test (`tests/test_plugin_frames.py`) confrontano il plugin con soluzioni analitiche (mensola 3D in
flessione, torsione e sforzo assiale; trave appoggiata con carico uniforme e trapezio; portale con la
formula delle rotazioni nodali), con i benchmark JSON del programma d'origine, con valori fissi
calcolati con il solutore d'origine (telaio di esempio, nucleo di esempio e pareti a mensola) e con
gli equilibri globali (reazioni uguali ai carichi su ogni caso, taglio alla base uguale alla forza
sismica). Le parti portate com'erano portano con sé i loro test (spettro, combinazioni, legno).
