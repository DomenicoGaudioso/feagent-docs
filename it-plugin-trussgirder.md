---
layout: default
title: "50 - Plugin TrussGirder"
parent: Italiano
nav_order: 50
---

# 50 - Plugin TrussGirder

Il plugin `trussgirder` porta in feagent le funzioni del programma Reticolare:
generazione parametrica di travi reticolari piane, analisi statica lineare, analisi
modale con masse nodali, pushover incrementale, import ed export del template Excel
e relazione Word. Il calcolo è **solo di feagent**: nessuna dipendenza da OpenSees né
dai repository d'origine.

## Come è calcolato

* Ogni asta è una biella (`Model.add_truss`) con rigidezza assiale `EA/L`; la
  reticolare giace nel piano XY e in ogni nodo sono bloccati i gradi di libertà fuori
  piano (`uz`, `rx`, `ry`, `rz`). I vincoli del foglio `restraints` agiscono su `ux` e `uy`.
* **Statica**: `Model.solve`. Le aste lavorano a solo sforzo normale `N` (positivo per
  trazione), quindi i risultati per asta sono `N`, la lunghezza, `σ = N/A` e il regime.
* **Modale**: `Model.modal`, con il vettore delle masse concentrate ricavato dal foglio
  `node_masses` (`mx` lungo X, `my` lungo Y). I gradi di libertà senza massa sono
  condensati da feagent. Si ottengono frequenze, periodi, forme e masse partecipanti.
* **Pushover**: solutore non lineare di materiale scritto sull'assemblaggio di feagent
  (file `pushover.py` del plugin). Geometria lineare (piccoli spostamenti), legame
  monoassiale delle aste con mappatura di ritorno. Il passo è risolto con Newton
  Raphson alla tangente, a controllo di spostamento (il moltiplicatore del carico è
  incognito) o di forza (moltiplicatore imposto). Se un passo non converge, per
  esempio per un meccanismo di collasso, l'analisi si ferma e restituisce i passi
  completati.

### Legge costitutiva delle aste

| Legame | Descrizione |
|---|---|
| `Elastic` | lineare, modulo `E` |
| `Steel01` | bilineare, snervamento `Fy`, pendenza post snervamento `b·E`, incrudimento cinematico, scarico elastico con pendenza `E` |
| `ElasticPP` | elastoplastico perfetto, snervamento a `Fy` (oppure a `eps_y·E`) |

## Unità di misura

I parametri delle azioni sono in **kN, m**, `E` e `fy` in MPa (nei fogli diventano
kN/m²), masse in t. I fogli letti da XLSX o passati in `modello` sono presi così come
sono: le unità devono essere coerenti fra loro (per esempio kN, m, `E` in kN/m², masse
in t).

## Azioni

Tutte le azioni ricevono il modello in uno di tre modi, in quest'ordine di precedenza:
`file` (workbook XLSX), `modello` (i fogli come oggetto JSON, come restituiti da `genera`
in `data["sheets"]`) oppure i parametri del generatore.

| Azione | Cosa fa | Uscite principali |
|---|---|---|
| `genera` | genera la reticolare con carichi, vincoli e masse | modello con i casi di carico, fogli del template |
| `statica` | analisi statica lineare | tabella di `N` per asta, reazioni, spostamenti, equilibrio, grafico di `N` nei correnti |
| `modale` | analisi modale con le masse nodali | frequenze, periodi, masse partecipanti, `nodes_masses`, forme |
| `pushover` | pushover a spostamento o a forza | curva di capacità (grafico), stadi, aste plasticizzate, `N` finale |
| `importa_xlsx` | legge il template Excel | modello e fogli |
| `esporta_xlsx` | scrive input e risultati in un XLSX | file `.xlsx` |
| `relazione` | relazione di calcolo Word | file `.docx` |

### Parametri del modello (comuni)

| Parametro | Tipo | Unità | Default | Note |
|---|---|---|---|---|
| `tipologia` | scelta | | `Warren` | Warren, Howe, Pratt, Mohniè (K), Nielsen, Parabolica, Parabolica rovescia, Diagonale doppia |
| `luce` | numero | m | 20 | |
| `altezza` | numero | m | 2,5 | in mezzeria per le paraboliche |
| `n_pannelli` | intero | | 8 | Warren richiede almeno 4 campi |
| `area_correnti` | numero | m² | 0,01 | |
| `area_anima` | numero | m² | 0,005 | montanti e diagonali |
| `E` | numero | MPa | 210000 | |
| `legame` | scelta | | `Elastic` | `Steel01`, `ElasticPP` per il pushover |
| `fy` | numero | MPa | 355 | solo legami non lineari |
| `incrudimento` | numero | | 0 | `b` di Steel01 |
| `massa_nodale` | numero | t | 1 | foglio `node_masses` |
| `carico_tipo` | scelta | | nodo di mezzeria superiore | oppure tutti i nodi del corrente superiore o inferiore |
| `carico_fy`, `carico_fx` | numero | kN | −10, 0 | per nodo, positivo concorde agli assi |
| `carichi` | tabella | kN | | righe `node_id, fx, fy`: sostituiscono i carichi generati |
| `caso_di_carico` | intero | | 1 | |
| `file`, `modello` | file, JSON | | | sorgenti alternative al generatore |

Parametri specifici: `modale` ha `n_modi` e `massa_default`; `pushover` ha `controllo`
(`spostamento` o `forza`), `nodo_controllo` (0 = nodo più caricato), `gdl_controllo`
(`uy` o `ux`), `spostamento_finale` (m, con segno), `fattore_finale` (moltiplicatore del
carico) e `passi`. Le azioni di analisi accettano `xlsx_out` per scrivere anche l'XLSX;
`esporta_xlsx` e `relazione` hanno `analisi` (`statica`, `statica+modale`,
`statica+pushover`, `statica+modale+pushover`) e `file_out`.

## Esempi

Da libreria:

```python
from feagent import plugins
p = plugins.get("trussgirder")

g = p.run("genera", tipologia="Pratt", luce=10, altezza=2, n_pannelli=6)
s = p.run("statica", modello=g.data["sheets"])
print(s.data["N_max_trazione"], s.data["N_max_compressione"])

m = p.run("modale", tipologia="Pratt", luce=30, altezza=3, n_pannelli=12, n_modi=4)
print(m.data["frequenze_Hz"])

po = p.run("pushover", tipologia="Pratt", luce=10, altezza=2, n_pannelli=6,
           controllo="forza", fattore_finale=4.0, passi=40)
print(po.data["aste_snervate"], po.data["lambda_finale"])

p.run("relazione", tipologia="Howe", luce=20, altezza=2.5, n_pannelli=8,
      analisi="statica+modale", file_out="relazione.docx")
```

Da riga di comando:

```bash
feagent plugin run trussgirder statica --param tipologia=Pratt --param luce=10 --param altezza=2 --param n_pannelli=6
feagent plugin run trussgirder esporta_xlsx --param file=modello.xlsx --param analisi=statica+modale --param file_out=risultati.xlsx
```

## Template Excel

Il workbook ha sette fogli e le stesse colonne del template del programma d'origine
(`template_input_truss2d.xlsx`, che è consegnato vuoto):

| Foglio | Colonne |
|---|---|
| `nodes` | `id, x, y, chord` |
| `elements` | `id, n1, n2, A, matTag, group` |
| `materials` | `matTag, type, E, Fy, b` |
| `load_cases` | `id, name` |
| `restraints` | `load_case_id, node_id, ux, uy` (1 = impedito) |
| `node_loads` | `load_case_id, node_id, fx, fy` |
| `node_masses` | `node_id, mx, my` |

`esporta_xlsx` aggiunge i fogli dei risultati `results_nodal`, `results_elements`,
`modal_frequencies`, `modal_shapes`, `pushover_curve`, `pushover_elements_final`,
`pushover_elements_history`.

## Cosa è stato portato e cosa no

* **Portati**: generatore e tipologie, statica, modale con masse nodali, pushover a
  spostamento e a forza con la stessa legge costitutiva, template Excel, relazione Word
  (rifatta nello stile aziendale, senza citare gli strumenti).
* **Non portato**: la pagina Streamlit (`app.py`) e gli endpoint HTTP, perché
  l'interfaccia è la GUI di feagent, che costruisce il modulo dai parametri dichiarati.
* **Corretto un difetto del generatore**: nella tipologia Parabolica il corrente
  superiore tocca quello inferiore agli appoggi e il programma d'origine lasciava lì due
  nodi distinti collegati solo da un montante di lunghezza nulla (scartato): la
  travatura era labile con due meccanismi (spostamenti dell'ordine di 1e14, mascherati
  dal solutore d'origine). Ora i due estremi sono un unico nodo e lo schema è isostatico.
  Le altre tipologie sono invariate.

## Validazione

I test (`tests/test_plugin_trussgirder.py`) confrontano:

* il **metodo dei nodi** (equilibrio dei nodi risolto a parte) con lo sforzo normale
  delle aste per Warren, Howe, Pratt e Parabolica, e l'equilibrio globale per tutte le
  tipologie;
* soluzioni chiuse: cavalletto, triangolo con spostamento per il lavoro virtuale, biella
  con massa, carico di collasso di una reticolare isostatica;
* i numeri del programma d'origine (riscontro una tantum con OpenSees): `N` e spostamenti
  per sette tipologie (scarto relativo sotto 1e-6), le prime quattro frequenze della
  Pratt (1e-6), il pushover Steel01 a spostamento e a forza (1e-7) e ElasticPP a
  spostamento (1e-7);
* la frequenza di una Warren con L = 30 m e H = 3 m contro la trave di Timoshenko
  equivalente (rigidezza flessionale dei correnti, rigidezza a taglio delle diagonali):
  scarto circa 1 %, tolleranza 3 %. La trave di Eulero Bernoulli senza deformabilità a
  taglio sovrastima la frequenza di circa il 14 %.

Nota sul pushover a forza con ElasticPP: nel punto esatto del carico di collasso il
programma d'origine si ferma un passo prima (non converge con la tangente nulla), mentre
qui il passo con le aste esattamente a snervamento converge; il carico limite è lo stesso.
