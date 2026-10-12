---
layout: default
title: "Plugin DigitalTwin"
parent: Italiano
nav_order: 51
---

# Plugin DigitalTwin (gemello digitale)

Il plugin `digitaltwin` e' il porting su feagent di **femDTO**: confronta un modello FEM con i dati
sperimentali di una struttura (frequenze, forme modali e smorzamenti da OMA, prove statiche) e ne
aggiorna i parametri (model updating). Il solutore e' sempre feagent: nessuna dipendenza da OpenSees.
L'interfaccia Streamlit dell'originale e' sostituita dalla GUI di feagent (menu Plugin), che costruisce
i moduli dai parametri delle azioni; le stesse azioni sono disponibili da libreria, CLI e IA.

```python
from feagent.plugins import get
dt = get("digitaltwin")
r = dt.run("calibra", modello="ponte.xlsx", obiettivo="frequenze",
           parametri=[{"gruppo": "section", "tag": "1", "parametro": "E", "min": 1.5e11, "max": 2.6e11}],
           frequenze_sper=[{"f_Hz": 3.2, "peso": 1}], metodo="DE", max_eval=500, seme=42)
r.model          # modello feagent aggiornato
r.tables[0]      # parametri calibrati
```

Dipendenze opzionali (extra): `scipy`, `pandas`, `openpyxl`, `pydantic`; `pymoo` e `optuna` solo per i
metodi NSGA-II, CMA-ES, PSO e TPE.

## Modelli in ingresso

| Sorgente | Fogli o chiavi | Note |
|---|---|---|
| JSON del DTO (`dictOPS`) | `StructuralAnalysisModel` | anche il JSON di `printModel` di OpenSees; gli appoggi mancanti si aggiungono con il parametro `vincoli` |
| Excel `ops` | `Point` o `Nodes`, `BeamEle`, `AreaEle`, `Material`, `Section`, `Supports`, `Rigid Link`, `Beam Load`, `Node Load` | formato di femDTO |
| Excel tipo MIDAS | `Nodes`, `Material`, `Section`, `Elements`, `Supports`, `Elastic Link`, `Beam End Release`, `Rigid Link`, `Beam Load` | unita' del file, densita' dalla colonna `Mass Density` |
| Excel tipo SAP2000 | `Joint Coordinates`, `Connectivity - Frame`, `Frame Props 01 - General`, restrizioni e carichi nodali | solo telai; aree e link non importati |
| Workbook feagent (`formato = feagent`, riconosciuto in `auto`) | `Node`, `Element`, `Material`, `Section`, `Support`, carichi, `Combination`, `Units` | quello di `Model.to_excel` o della GUI, per esempio il ponte del plugin `bridge`; nessuna conversione: il modello resta feagent |
| `feagent.Model` | oggetto passato come `modello` da codice Python | unita' N, m; il modello originale non viene modificato |

Assi locali delle travi: la terna e' quella di OpenSees (`vecInLocXZPlane` e' l'asse z locale), con
`Iy` attorno a y e `Iz` attorno a z. I lettori Excel mettono z verso l'alto (come Midas e come il JSON
di esempio), oppure y verso l'alto per SAP2000; `orientamento = terna` replica la terna di Gram-Schmidt
dell'originale. Mappatura degli elementi: `ElasticBeam3d` in trave di Eulero-Bernoulli,
`ForceBeamColumn3d` in trave con sezione elastica (Timoshenko se ci sono `Avy` e `Avz`), `ShellDKGQ` in
guscio Q4 o T3, `ZeroLength` e `TwoNodeLink` in appoggi elastici a terra o legami rigidi, `rigidLink` in
`add_rigid_link`, `massperlength` in massa lineare della sezione. Una struttura labile viene
stabilizzata con molle debolissime a terra e segnalata nei messaggi.

I link elastici MIDAS di tipo GEN sono importati come legami rigidi sui gradi di liberta' indicati
(colonna `RIGID`) negli assi globali; l'angolo del link e le molle con rigidezza finita fra nodi liberi
non sono rappresentati e vengono segnalati.

## Azioni

| Azione | Cosa fa |
|---|---|
| `importa_modello` | legge JSON o Excel o un modello feagent, costruisce il modello, riepiloga elementi e load pattern (per feagent, materiali e sezioni con i nomi da usare nei parametri), salva il `dictOPS` o il workbook |
| `modale` | frequenze, periodi e masse efficaci |
| `confronto_sperimentale` | frequenze misurate e calcolate, scarti, MAC, associazione dei modi (algoritmo ungherese) |
| `sensibilita` | indici di Sobol `S1` e `ST` (e `S2`) delle prime frequenze rispetto ai parametri |
| `calibra` | model updating su frequenze, OMA (frequenze e forme) o spostamenti statici; il modello aggiornato e' `Result.model` |
| `statica` | combinazione di load pattern: spostamenti, reazioni, sollecitazioni massime |
| `buckling` | moltiplicatori critici sotto una condizione statica: casi di carico con fattori e, per i modelli feagent, il convoglio fermo |
| `treno_statico` | modelli feagent a graticcio: convoglio fermo (o nella posizione piu' sfavorevole) su una trave principale, aggiunto al modello come caso di carico |
| `passaggio_treno` | convoglio ferroviario a velocita' variabile, Newmark con smorzamento di Rayleigh, binari multipli (modelli dictOPS e travi; non per i graticci feagent) |
| `scenari_danno` | rimozione di elementi o degrado di un parametro, frequenze degli scenari |
| `monitoraggio` | FFT, PSD di Welch, filtro passa-banda, picchi e smorzamento a semi-potenza da accelerometri |

### Parametri da calibrare

Tabella con colonne `gruppo`, `tag`, `parametro`, `min`, `max`. Per i modelli dictOPS i gruppi sono `material`, `section`,
`beamEle`, `node` (massa nodale), `nodeLoad` e `beamLoad`; i `tag` sono id o intervalli (`1,3,10:20`).
Il valore e' assoluto, oppure un moltiplicatore con `limiti = relativi`. Una variazione di sezione si
propaga agli elementi che la usano; una di materiale a sezioni ed elementi che ne dipendono (il
coefficiente di Poisson resta, quindi cambia anche G).


### Modelli feagent: parametri per nome

Per un workbook o un `Model` feagent i gruppi si riferiscono alle entita' **per nome**, quello dei fogli
`Material` e `Section` (senza nome: `MAT<n>`, `SEC<n>`). La tabella `Materiali` e la tabella `Sezioni` di
`importa_modello` riportano nomi, proprieta', numero di elementi e id degli elementi.

| gruppo | tag | parametro |
|---|---|---|
| `materiale` | nome o numero d'ordine del materiale (`Acciao`, `C.a.`) | `E`, `G`, `nu`, `rho`, `alpha` |
| `sezione` | nome della sezione, anche con jolly (`Soletta`, `Cordolo*`, `C0?`) | `A`, `Iy`, `Iz`, `J`, `Asy`, `Asz`, `mass_per_length`; inoltre `E`, `G`, `nu`, `rho` sul solo materiale degli elementi di quella sezione |
| `elemento` | id degli elementi, `1:200,305` (travi, bielle, gusci) | come `sezione` |
| `molla` | nodi con molla elastica a terra (parametro `ux`..`rz`), oppure id di una molla (parametro `k`) | rigidezza |
| `massa` | caso di carico che genera massa (deve stare in `massa_da_carichi`) | `fattore` |

Il modello da calibrare non viene modificato: ogni valutazione lavora su una copia di lavoro con i valori
cambiati (materiale o sezione copiati solo per gli elementi interessati) che viene ripristinata prima della
valutazione successiva, senza copiare il modello a ogni passo. Cambiando `E` o `nu`, `G` segue
`E / (2 (1 + nu))` se il materiale era isotropo. Il modello aggiornato (`Result.model`) conserva nodi,
vincoli, molle, carichi, casi, combinazioni e il foglio `Units` (`Result.units`); `salva_json` con
estensione `.xlsx` lo scrive come workbook. La massa e' quella della densita' `rho` piu' quella dei casi
in `massa_da_carichi`: per il ponte del plugin `bridge` si indica solo `G2`, perche' il peso proprio `G1` e'
gia' nella densita' degli elementi (vedi la protezione contro la massa contata due volte piu' sotto). L'unita' di lunghezza del foglio `Units` fissa la gravita' usata per
trasformare i casi di carico in massa.

#### Dal ponte generato al ponte calibrato

```python
from feagent.plugins import get
from feagent.plugins.runner import run_and_save

run_and_save("bridge", "genera", {"tipologia": "misto_2travi"}, "ponte.xlsx")        # 30, 40, 30 m
dt = get("digitaltwin")
r = dt.run("calibra", modello="ponte.xlsx", salva_json="ponte_calibrato.xlsx",
           massa_da_carichi=[{"pattern": "G2", "fattore": 1}],
           parametri=[{"gruppo": "materiale", "tag": "Acciao", "parametro": "E", "min": 1.7e11, "max": 2.5e11}],
           frequenze_sper=[{"f_Hz": f, "peso": 1} for f in [1.1739, 1.4321, 1.7956, 2.0683, 2.2635, 2.6410]])
```

#### Convoglio statico e buckling sul ponte a graticcio

Il passaggio dinamico presuppone una catena di travi e **non e' previsto per un graticcio feagent** (travi
longitudinali, traversi, strisce di soletta): `passaggio_treno` su un ponte del plugin `bridge` risponde con un
messaggio che rimanda a `treno_statico`. Questa azione posiziona il convoglio (catalogo del plugin: `ETR500`,
`Modello1`, ...; catalogo `feagent.vehicles`: `LM1_TS1`, `LM71`, `HL93_TRUCK`, ...; oppure `personalizzato` con la
tabella `interasse_m`, `peso_kN`) sulla **linea longitudinale** scelta e lo aggiunge al modello come caso di
carico `TRENO`, con carichi concentrati sugli elementi della linea.

* `linea`: vuoto = una trave principale (fra gli elementi longitudinali che formano una catena lunga almeno
  meta' del modello, la sezione di rigidezza flessionale maggiore, `Trave`, e fra le sue catene quella a
  coordinata trasversale minore). Per sceglierne un'altra: nome della sezione (anche con jolly), `y=3` (la catena
  piu' vicina a quella coordinata trasversale), `n=2` (la seconda in ordine di coordinata), separati da `;`.
  Gli estremi della linea devono essere nodi vincolati, altrimenti c'e' un avviso.
* `posizione`: ascissa del primo asse lungo la linea. Vuoto o `critica`: la posizione che massimizza lo
  spostamento verticale del modello, cercata con una scansione statica del carico mobile (al piu' 400 posizioni,
  poi un affinamento di 41 posizioni attorno al massimo).
* Il risultato riporta lo spostamento massimo del caso applicato e quello dell'inviluppo statico di una scansione
  indipendente piu' fine; `Result.model` contiene il caso `TRENO`, che si passa come `modello` a `statica`,
  `buckling` e `calibra` (bersagli statici), oppure si salva con `salva_json`. `statica` e `buckling` accettano
  anche direttamente `convoglio`, `linea`, `posizione`.

`buckling` lavora su una condizione statica: `carichi` (casi del modello con i loro fattori, per esempio
`G1 + G2`) e, se indicato, il convoglio fermo. Un ponte appoggiato non ha compressioni sotto i pesi e sotto il
convoglio verticale: non c'e' nessun moltiplicatore positivo e il risultato lo dice con un avviso (non un errore);
un moltiplicatore enorme (oltre 1000) e' segnalato allo stesso modo. Per studiare l'instabilita' si aggiunge un
caso con compressione assiale.

| Prova | Riferimento | Misurato |
|---|---|---|
| colonna di Eulero cerniera, cerniera, 6 m, EI = 84 MN m2 | P_cr = pi2 EI / L2 = 23 029,08 kN | 23 029,23 kN (0,0007 %) |
| colonna incastro, cerniera | lunghezza libera 0,6992 L | entro 0,02 % |
| ponte 30 m, G1 + G2 | nessuna compressione | nessun moltiplicatore, avviso |
| ponte 30 m, 1 MN di compressione a ogni estremo delle due travi | | lambda_cr = 68,18 (136 MN in totale), convoglio verticale aggiunto: invariato |
| convoglio `LM1_TS1` sulla trave `Trave` (y = -3), posizione critica, testa a 15,6 m | inviluppo statico di `moving_load_analysis` (300 posizioni) | 0,054 370 m contro 0,054 369 m (0,001 %) |
| convoglio `LM71`, testa a 17,4 m | stesso | 0,088 946 m contro 0,088 945 m (0,001 %) |
| un asse da 100 kN su trave semplice di 30 m | P L3 / (48 E I) | scarto sotto 0,2 % |

Tempi sul ponte a una campata (macchina condivisa): `treno_statico` 4 s per `LM1_TS1` (415 posizioni risolte),
4 s per `LM71`, 7 s per `ETR500` (941 posizioni).

#### Massa contata due volte

Se il modello ha la densita' `rho` e un caso di `massa_da_carichi` ha la risultante verticale pari al peso
proprio da `rho` (entro il 5 %, con avviso anche fino al 20 % di scarto), la massa e' praticamente doppia. Ogni
azione con `massa_da_carichi` emette allora un avviso con la massa propria, la massa del caso, il rapporto, la
massa totale risultante e quanto escono basse le frequenze. `escludi_peso_proprio` (default falso)
esclude in automatico quei casi dal contributo di massa. Sul ponte a 30 m: `G2` da solo (54 t contro 394 t
di massa propria) non da' avvisi, f2 1,950 Hz; `G1 + G2` da' l'avviso (G1 = 337 t, massa propria da `rho` 342 t,
rapporto 0,985) e f2 scende a 1,424 Hz (-27 %); con `escludi_peso_proprio` torna 1,950 Hz.
Nei misti a due travi e a graticcio la massa da `rho` coincide con `G1` entro 1,5 %, dopo aver corretto la larghezza
delle strisce di cordolo (passo fra le stazioni, non la lunghezza del cordolo). Nel cassone e nel c.a. la soletta fra
le travi non ha elementi: il suo peso e' solo in `G1` e la massa da `rho` e' inferiore (13 % e 6 %), quindi
`escludi_peso_proprio` toglierebbe massa vera: per quei ponti la massa va decisa caso per caso.

#### Verifiche sul ponte a una campata (30 m)

Misure con `massa_da_carichi = G2`. Le frequenze assolute di questo paragrafo (1,483, 1,809, 4,065, 4,092 Hz) sono state
misurate prima della correzione delle strisce di cordolo; ora sono 1,689, 1,950, 4,350, 5,002 Hz. I confronti relativi
(recupero dei parametri, scarti, indici di Sobol) non cambiano e i test li verificano.

| Azione | Prova | Esito |
|---|---|---|
| `sensibilita` | `E` dell'acciaio, `E` di `Soletta` e di `Cordolo` (misurato prima che i cordoli diventassero la stessa sezione della soletta; ora la prova usa `Traverso`), N = 32 (160 analisi, 8,7 s) | ST di f1: acciaio 0,872, soletta 0,142, cordoli 4e-7; ST di f3: 0,278, 0,812, 2,5e-5. Con N = 32 qualche S1 supera 1 (1,036): rumore dello stimatore |
| `calibra` statica | bersagli: spostamenti a mezzeria con `E` dell'acciaio +10 %, caso `G1 + G2` | recuperato +10,005 % (105 valutazioni) |
| `calibra` statica | stessi bersagli con `E` della `Soletta` +10 % | **non identificabile**: gli spostamenti a mezzeria non cambiano (variazione relativa 1e-9); il valore calibrato e' arbitrario e ora c'e' un avviso |
| `calibra` OMA | frequenze e forme di 4 modi visibili a 12 sensori verticali (travi e cordoli), modello con acciaio +6 % e soletta +10 % | acciaio +5,992 %, soletta +9,956 %; MAC 1,0000 sui 4 modi; scarti sulle frequenze sotto 0,02 %; 420 valutazioni, 40 s |
| `scenari_danno` | rimozione di un elemento di una trave a mezzeria | f1 da 1,483 a 0,550 Hz (-63 %) |
| `scenari_danno` | rimozione di tutte le travi a mezzeria | struttura divisa, frequenze nulle: avviso |
| `scenari_danno` | degrado di `Iy` delle travi fino a -30 % | f2 -15,5 % (la radice del 70 % darebbe -16,3 %) |
| `monitoraggio` | accelerometro sintetico a 50 Hz, 900 s, le prime 4 frequenze con rumore bianco di pari ampiezza | picchi 1,4832, 1,8066, 4,0649, 4,0894 Hz contro 1,4829, 1,8092, 4,0654, 4,0922 Hz (scarti 0,02, 0,14, 0,01, 0,07 %) |

Difetti trovati e corretti: la calibrazione avverte ora quando un parametro non e' identificabile
(l'obiettivo non cambia fra i due limiti); il confronto OMA avverte quando i sensori misurano meno del 5 %
dello spostamento massimo di un modo (nel ponte i modi trasversali non si vedono con sensori verticali: la
forma e' rumore normalizzato e il MAC non ha significato); gli scenari con frequenze nulle (struttura labile)
sono segnalati; la statica di un graticcio da 1000 gradi di liberta' usava la LU densa (0,8 s a valutazione,
0,04 s con il solutore sparso).

#### Caso di prova: ponte misto a due travi, 30 + 40 + 30 m

Il ponte generato dal plugin `bridge` (`misto_2travi`, 606 nodi, 768 travi, 3636 gradi di liberta', materiali
`Acciao` e `C.a.`, sezioni `Trave`, `Soletta`, `Sbalzo`, `Traverso`; i cordoli hanno la stessa sezione della soletta e si distinguono solo per id di elemento) si calibra con `massa_da_carichi = G2`.
Frequenze nominali dei primi sei modi: 1,1344, 1,3676, 1,7304, 1,9897, 2,1693, 2,5387 Hz. Le frequenze
"misurate" sono quelle dello stesso modello con un parametro aumentato del 10 %, e la calibrazione parte dal
modello nominale (DE, seme 42, `max_eval` 600, limiti 170 e 250 GPa per l'acciaio, 27 e 41 GPa per la soletta):

| Parametro perturbato | Frequenze misurate [Hz] | Valore recuperato | Scarto sul +10 % | Valutazioni | Tempo |
|---|---|---|---|---|---|
| `E` dell'acciaio (210 GPa) | 1,1739, 1,4321, 1,7956, 2,0683, 2,2635, 2,6410 | 231,000 GPa | +9,999998 % | 240 | 33 s |
| `E` della sezione `Soletta` (34,077 GPa) | 1,1505, 1,3697, 1,7498, 2,0080, 2,1793, 2,5571 | 37,4847 GPa | +9,99999 % | 225 | 36 s |
| entrambi, calibrati insieme | 1,1897, 1,4344, 1,8148, 2,0867, 2,2751, 2,6625 | 231,005 e 37,482 GPa | +10,003 % e +9,991 % | 630 | 119 s prima, 65 s con la copia di lavoro ripristinata |

Gli scarti residui sulle frequenze sono sotto 0,001 %. La copia di lavoro ripristinata al posto del `deepcopy`
per valutazione ha dato gli stessi parametri (identici al bit) e lo stesso obiettivo (differenza 3e-18); le
valutazioni in parallelo (`workers` di scipy) non sono usate perche' la funzione obiettivo e' una chiusura non
serializzabile e sotto Windows il costo dei processi supera il guadagno. Una valutazione (matrici, analisi
modale sparsa) costa circa 0,10 s (prima 0,19 s); la macchina di prova era condivisa. Sensibilita' di Sobol
(N = 32, 160 analisi modali, 30 s), indici totali ST sulle prime cinque frequenze: acciaio 0,85 1,03 0,93 0,96 1,01,
`Soletta` 0,16 0,00 0,09 0,06 0,02, `Cordolo` 0,00: la rigidezza dei cordoli non e' identificabile dalle frequenze,
quella della soletta lo e' poco (un `E` che pesa cosi' poco richiede frequenze misurate accurate).
### Dati sperimentali

* `frequenze_sper`: `f_Hz`, `peso`, `xi_%` (lo smorzamento e' solo riportato nel confronto);
* `oma`: una riga per modo e sensore, con `modo`, `f_Hz`, `nodo`, `dir` (x, y, z), `valore`;
  in alternativa `oma_file` (Excel di femDTO: prima riga le frequenze, un sensore per riga, un modo per
  colonna) e `corr_file` (CSV `sensorID;femID`);
* `statica_bersagli`: `nodo`, `gdl` (1 a 6), `target`, `peso`, oppure `statica_file` (foglio `nodeDisp`).

### Funzioni obiettivo

Frequenze: $E=\sum_j w_j\left(\frac{f_{exp,j}-f_{FEM,i(j)}}{f_{exp,j}}\right)^2$. OMA:
$E=\sum_j \alpha_j\,\Delta f_j^2+\beta_j\,(1-MAC_{jj})$. Statica:
$E=\sum w\,((u-u_t)/u_t)^2$. I modi sono associati con l'algoritmo ungherese sulla matrice dei costi.
Rispetto a femDTO la matrice dei costi non arrotonda piu' a 5 decimali, i pesi seguono il modo
sperimentale e un modo senza controparte costa il suo peso.

### Metodi di ottimizzazione

`DE`, `DualAnnealing`, `DIRECT`, `Powell`, `Nelder-Mead` (scipy), `NSGA-II`, `CMA-ES`, `PSO` (pymoo),
`TPE` (optuna), con seme fisso e storia di convergenza. Se la libreria manca l'errore lo dice.

## Cosa e' stato portato e cosa no

Portati: schema DTO, lettura Excel e JSON, MAC e confronto modale, funzioni obiettivo, dispatcher di
ottimizzazione, sensibilita' di Sobol (stimatori di Saltelli e Jansen su sequenza di Sobol di scipy, al
posto di SALib), passaggio del treno, scenari di danno, filtri e spettri. Non portati: l'interfaccia
Streamlit e i grafici pyvista (la visualizzazione e' la GUI di feagent), l'analisi ambientale con rumore
bianco e quella sismica con accelerogrammi (feagent ha gia' le analisi in storia temporale), i video.

## Validazione

I test (`tests/test_plugin_digitaltwin.py`) confrontano frequenze di travi appoggiate e a mensola con le
formule chiuse (scarto sotto lo 0,3 %), il carico di Eulero (0,5 %), la freccia in mezzeria, il passaggio
lento di un treno con l'inviluppo delle frecce statiche (3 %), gli indici di Sobol della funzione di
Ishigami e di funzioni lineari, e il recupero di moduli elastici noti con frequenze generate dal modello.
Un confronto una tantum con OpenSees su una trave appoggiata di 20 elementi da' le stesse prime quattro
frequenze (0,90272 e 3,61085 Hz) a otto cifre.
