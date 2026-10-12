---
layout: default
title: "47 - Plugin Bridge"
parent: Italiano
nav_order: 47
---

# 47 - Plugin Bridge

Il plugin `bridge` costruisce il **modello a graticcio piano di un impalcato da
ponte** direttamente come `feagent.Model`: travi principali, traversi, strisce di
soletta, cordoli, appoggi, sezioni, sezioni composte a fasi, selle Gerber e carichi
permanenti. Il calcolo è **solo di feagent**: nessuna dipendenza da OpenSees né dai
repository d'origine.

La parte di modellazione è portata da
[BridgeBIM](https://github.com/DomenicoGaudioso/BridgeBIM) (i quattro `src_fem.py`
delle tipologie, `assi_travi.py` e i parametri di default). Di BridgeBIM **non** si
porta nulla di IFC, CAD, Tekla, Revit o computo: serve solo il modello analitico, che
nell'originale veniva scritto in un Excel per MIDAS e che qui diventa un modello
feagent pronto da risolvere.

## Tipologie

| `tipologia` | Impalcato | Note |
|---|---|---|
| `misto_2travi` | acciaio-calcestruzzo a due travi | longherina di spina opzionale, controventi di diaframma opzionali, sezioni composte a fasi |
| `misto_graticcio` | acciaio-calcestruzzo a `n_travi` travi | stesse opzioni delle due travi; con `n_travi = 2` coincide con `misto_2travi` |
| `misto_cassone` | acciaio-calcestruzzo a cassone | due anime, asse dai cigli rientrati degli offset |
| `ca_rettangolare` | calcestruzzo armato a travi rettangolari | `nt` travi a interasse costante, traversi in c.a., selle Gerber come cerniere |

## Come è costruito il modello

Il graticcio è **piano**: tutti i nodi stanno a `z = 0` e l'asse del ponte segue il
foglio `As`. Per ogni **stazione** lungo l'asse (estremi dei conci, traversi, appoggi,
più i punti intermedi con passo massimo `passo_max_trasv`) si creano i nodi della
sezione trasversale, da sinistra a destra:

`punta del cordolo sx`, `ciglio sx`, `trave sx` (con le eventuali travi intermedie),
`trave dx`, `ciglio dx`, `punta del cordolo dx`.

Gli elementi sono tutti travi di feagent:

* **travi longitudinali**, una sezione per concio (acciaio: doppio T saldato dalle
  lamiere `psup`, `sa`, `pinf`; c.a.: rettangolo `B x H` con `H` lineare nel concio);
* **traversi** alle stazioni dei traversi, **strisce di soletta** altrove (e anche sotto
  i traversi nelle tipologie miste a due travi e a graticcio), **sbalzi** fra ciglio e
  trave esterna, **cordoli** fra la punta e il ciglio;
* le strisce di soletta hanno sezione `interasse fra le stazioni x spessore`: rappresentano
  la soletta nella ripartizione trasversale, la loro rigidezza è quella dello strato di
  soletta di una stazione;
* **longherina** (`con_longherina`), **controventi** di diaframma e di piano
  (`con_controventi`, nodi bassi e link rigidi) nelle tipologie a due travi e a graticcio;
* **selle Gerber** nel c.a.: una cerniera flessionale (rilascio di `My`) per trave a ogni
  giunto, su una sola estremità per non rendere labile il tratto.

I vincoli sono quelli dell'originale: verticale su tutti gli appoggi, ritegni trasversali e
longitudinali che lasciano libere le dilatazioni (nel c.a. un solo punto fisso longitudinale su
tutto il ponte, nel misto un ritegno longitudinale su due appoggi diversi).

### Sezioni composte a fasi

Con `con_fasi` il foglio `Section` porta anche le righe delle fasi (colonna `group`, schema
di WOBridge): `group 0` trave d'acciaio, `1` composta a lungo termine con `fasi_nL`, `2`
ritiro, `3` cedimenti, `4` breve termine con `fasi_n0`. La larghezza collaborante della
soletta è `fasi_beff` (m) e lo spessore `fasi_tslab` (m); con `fasi_crack` (m) le sezioni
entro quella distanza dagli appoggi interni sono fessurate (acciaio più armatura
`fasi_fi` a passo `fasi_passo`). In feagent diventano i *section group* `group1` ... `group4`
e il load case sceglie il gruppo dal nome (`G1` sulla sola trave d'acciaio, `G2` sul lungo
termine), come in WOBridge.

## Convenzioni conservate dall'originale

* assi globali: `x` longitudinale lungo l'asse del ponte, `y` trasversale con il ciglio
  sinistro a `y < 0` e il destro a `y > 0`, `z` verticale; il graticcio è piano (`z = 0`);
* le lunghezze dei **parametri** sono in mm (`spessore_soletta`, `larghezza_cordolo`,
  `offset_sx`, ...), le luci delle campate e i fogli `As` e `At` in m;
* le proprietà sezionali sono quelle dell'originale: torsione del rettangolo come costante
  di Saint Venant, area di taglio `Asz` all'anima e `Asy` alle ali (asse locale `z`
  verticale), momento flettente verticale con `Iyy`, rilasci su `My`;
* l'orientazione degli assi locali è quella di WOBridge (`geomTransf Linear` con il vettore
  `ey` come `vecxz`), la stessa del lettore `feagent.io_wobridge`;
* la regola per l'asse delle travi è quella di `assi_travi`: dal foglio `At` per le due travi
  e il graticcio, dai cigli `P1`/`P4` rientrati degli offset per il cassone, a interasse `int`
  per il c.a.;
* i numeri di nodo e di elemento sono quelli dell'originale, passo per passo.

### Dove il plugin si discosta dall'originale

| Punto | Originale | Plugin |
|---|---|---|
| Unità | kN e m (`E` in kN/m²) | **SI, N e m** (`E` in N/m²), perché un `feagent.Model` non porta l'unità e la GUI legge N e m |
| Carichi | nessuno: solo geometria, sezioni e vincoli | casi `G1` e `G2` (vedi sotto) |
| Cassone, strisce di soletta | larghe quanto la luce trasversale (circa otto volte troppo rigide) | larghe quanto l'interasse fra le stazioni, come le altre tipologie (`strisce_tributarie`, di default vero; falso riproduce l'originale) |
| C.a. con numero dispari di travi | `assi_travi.offsets_interasse` scriveva `-nt // 2`, che è `(-nt) // 2`: le travi risultavano decentrate di un interasse (3 travi a `-2i, -i, 0`) | `-(nt // 2)`: 3 travi a `-i, 0, +i` |
| Densità | colonna `Density` in kN/m³ | `gamma` in N/m³ e `rho` dalla colonna `Mass`, per l'analisi modale |

Per il resto le tabelle prodotte coincidono con quelle del generatore originale cella per
cella (verificato sui casi di studio di BridgeBIM e sui ponti di esempio).

## Carichi permanenti

Sono un'aggiunta del plugin (l'originale non applicava carichi). Due load case:

* **`G1`, peso proprio**: travi, traversi, longherine e controventi pesano per `gamma A`
  (acciaio maggiorato del coefficiente `maggiorazione_acciaio`, di default 1,10 per
  bulloni, pioli, giunti e controventi di montaggio); la soletta (`peso_cls`, spessore
  `spessore_soletta`) e i cordoli (spessore pari alla soletta) sono carichi lineari sugli
  elementi trasversali, con larghezza tributaria mezzo campo per lato, perché le loro strisce
  sono elementi fittizi di rigidezza (la sezione di soletta e di cordolo ha larghezza pari al passo fra le stazioni, per cui la massa da `rho` coincide con `G1`); i link rigidi non pesano;
* **`G2`, permanenti portati**: pavimentazione (`peso_asfalto`, `spessore_asfalto`, fra i cigli)
  ed eventuali barriere (`barriera`, kN/m per lato, forze nodali sui cordoli).

I totali verticali sono nel risultato (`data["totali_kN"]`) e coincidono con il volume reale
per il peso specifico: la pavimentazione è `22 kN/m³ x spessore x larghezza ciglio ciglio x
lunghezza`. Le combinazioni proposte in `Result.combinations` sono `SLE_rara` (1 e 1) e
`SLU_permanenti` (1,35 e 1,5).

## Fogli d'ingresso (stesso Excel di BridgeBIM)

| Foglio | Contenuto |
|---|---|
| `Conci` | una riga per concio: `Lunghezza` (m), `Hstart`, `Hend` (mm), `sa` (anima), `psup`, `pinf` (piattabande, es. `600x30` o `600x30+400x20`); per il c.a. anche `B`, `nt`, `int`, `gerber` (`SI`/`NO`) con `psup` = larghezza della trave |
| `Campate` | luci in m (prima colonna): danno le linee di appoggio |
| `As` | asse e cigli della soletta: `X1..Z5`, `P1` ciglio sx, `P4` ciglio dx, `P5` asse (m) |
| `At` | assi delle travi di bordo (`P1`, `P4`); se manca vale `As` |
| `traversi` | `prog` (passo dal traverso precedente, m), `Hstart`, `Hend`, `sa`, `psup`, `pinf` e gli stessi con suffisso `_rs` / `_rd` per gli sbalzi |

La tipologia, con `auto`, si deduce dal foglio `Conci`: le colonne `B`, `nt`, `int` dicono
c.a.; la colonna `Fondo` o i fogli `controventi_W` e `traversi_inf` dicono cassone; il resto
è a travi (due, o `n_travi`).

## Azioni

| Azione | Cosa fa | Uscite principali |
|---|---|---|
| `leggi_excel` | legge l'Excel di BridgeBIM e genera il modello | modello con `G1` e `G2`, riepilogo |
| `genera` | costruisce un ponte rettilineo dai parametri principali, senza Excel | modello con `G1` e `G2`, riepilogo |
| `modello_di_esempio` | il ponte di esempio della tipologia, con barriere da 1,5 kN/m | modello, riepilogo |
| `controllo` | analisi statica di prova: equilibrio, reazioni, frecce | tabelle di equilibrio e reazioni, grafico della freccia, avvisi |

### Parametri di `genera`

| Parametro | Tipo | Unità | Default | Note |
|---|---|---|---|---|
| `tipologia` | scelta | | `misto_2travi` | vedi sopra |
| `campate` | testo | m | `30, 40, 30` | luci separate da virgola |
| `larghezza` | numero | m | 10 | soletta, ciglio ciglio |
| `interasse` | numero | m | 6 | travi di bordo, anime del cassone, travi in c.a. |
| `altezza` | numero | mm | 1800 | trave metallica o trave in c.a. |
| `n_travi` | intero | | 3 | graticcio e c.a. |
| `passo_traversi` | numero | m | 5 | |
| `gerber_lunghezza` | numero | m | 0 | campata sospesa al centro dell'ultima campata centrale (c.a.) |
| `spessore_soletta` | numero | mm | 280 | |
| `spessore_asfalto` | numero | mm | 80 | |
| `larghezza_cordolo` | numero | mm | 1700 | |
| `barriera` | numero | kN/m | 0 | per lato, in `G2` |
| `passo_max_trasv` | numero | m | 1 | |
| `con_longherina` | booleano | | falso | due travi e graticcio |
| `con_fasi` | booleano | | falso | misto: sezioni composte a fasi |
| `parametri` | JSON | | | qualsiasi parametro del generatore (mm): `offset_sx`, `offset_dx`, `con_controventi`, `fasi_beff`, `fasi_nL`, `peso_asfalto`, `maggiorazione_acciaio`, `classe_cls_travi`, ... |

`leggi_excel` ha `file`, `tipologia` (`auto`), `carichi` e `parametri`; `controllo` ha `file`
(vuoto: ponte di esempio), `tipologia` e `parametri`.

## Esempi

Da libreria:

```python
from feagent import plugins
p = plugins.get("bridge")

r = p.run("genera", tipologia="misto_2travi", campate="30, 40, 30",
          larghezza=10, interasse=6, altezza=1800, barriera=1.5)
m = r.model                          # feagent.Model in N e m, casi G1 e G2
res = m.solve(sparse=True, cases={"G1": 1.0, "G2": 1.0})

r = p.run("leggi_excel", file="input_cassone_01.xlsx")     # tipologia dedotta
r = p.run("controllo", tipologia="ca_rettangolare")        # equilibrio e freccia
print(r.data["uz_min_mm"])
```

Da riga di comando:

```bash
feagent plugin run bridge modello_di_esempio --param tipologia=misto_cassone
feagent plugin run bridge leggi_excel --param file=input_2travi_01.xlsx
```

## Verifica

I test (`tests/test_plugin_bridge.py`) controllano:

* le tabelle contro il codice originale di BridgeBIM, eseguito una tantum sugli stessi fogli
  (nodi, elementi, sezioni, vincoli e somme delle proprietà, cella per cella);
* l'equilibrio (somma delle reazioni uguale alla somma dei carichi) e i totali di `G1` e `G2`
  dalla geometria;
* la freccia di una trave appoggiata da 30 m, `5 q L⁴ / 384 E I` con `I` calcolata dalle lamiere,
  entro l'1 per cento (il resto è la discretizzazione del carico sui nodi);
* le cerniere Gerber (momento nullo al rilascio, non nullo senza rilascio);
* la lettura da Excel e la generazione dai parametri, che danno lo stesso modello.

Controllo incrociato una tantum con OpenSees (non è una dipendenza): sui modelli di esempio a
due travi e a graticcio gli spostamenti nodali coincidono con quelli di feagent a `1e-10`
(relativo allo spostamento massimo), con le stesse forze nodali equivalenti.

## Limiti

* il graticcio è piano: la torsione e la ripartizione trasversale sono quelle delle strisce, non
  una piastra; non c'è effetto dell'altezza della trave sul piano del modello (i controventi
  hanno i nodi bassi a quota `-h` e vincoli sui nodi bassi, come nell'originale);
* nel c.a. il peso dei traversi si somma a quello della soletta senza togliere la parte di anima
  che sta nello spessore della soletta (differenza piccola, a favore di sicurezza);
* nessun carico mobile né azione variabile: il plugin fornisce il modello e i permanenti, i
  carichi mobili si possono aggiungere con il modulo dei carichi mobili di feagent;
* nessun export IFC, CAD, Tekla, Revit né computo, che restano in BridgeBIM;
* il ponte generato dai parametri è rettilineo e a quota costante; i ponti in curva o in
  pendenza si descrivono con l'Excel (fogli `As` e `At`).
