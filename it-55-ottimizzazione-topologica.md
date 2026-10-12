---
layout: default
title: "55 - Ottimizzazione topologica"
parent: Italiano
nav_order: 55
---

# 55 - Ottimizzazione topologica

`feagent.topology` toglie il materiale dove non serve. Si parte da un modello
già costruito (mesh di solidi o di gusci, vincoli, carichi) e si cerca la
distribuzione di materiale più rigida possibile con una frazione di volume
data, cioè la **minima deformabilità** (compliance) `c = Fᵀu`. Dal risultato si
ricava il **modello ridotto**, senza gli elementi tolti, che si rianalizza come
qualunque altro modello feagent.

Il modulo è scritto tutto in Python (numpy e scipy), come il resto del motore.

## Metodi

**SIMP** (densità continua penalizzata). Ogni elemento ha una densità
`0 ≤ ρ ≤ 1` e un modulo

*E(ρ) = E_min + ρ^p (E₀ − E_min)*, con *p = 3* e *E_min = 10⁻⁹ E₀*.

La sensibilità della deformabilità è *∂c/∂ρₑ = −p ρₑ^(p−1) (E₀ − E_min) uₑᵀ kₑ uₑ*.
Si filtra con il filtro di densità a cono di raggio `r` (Bruns e Tortorelli
2001). Le densità si aggiornano con il criterio di ottimalità, con bisezione
sul moltiplicatore del volume e limite di variazione 0,2 per iterazione. È la
formulazione del codice a 88 righe di Andreassen et al. (SMO 43, 2011).

**BESO** (ESO bidirezionale, *soft-kill*). Gli elementi sono pieni (`x = 1`)
oppure quasi vuoti (`x = x_min = 10⁻³`, modulo `x_min^p E₀ = 10⁻⁹ E₀`). Si parte
dalla struttura piena e a ogni iterazione il volume cala del rapporto evolutivo
fino alla frazione chiesta; gli elementi si tolgono e si rimettono secondo il
loro numero di sensibilità (Huang e Xie, *Evolutionary Topology Optimization of
Continuum Structures*, 2010). Il disegno è già netto e non richiede soglie.

### BESO passo per passo

A ogni iterazione *k*:

1. **Analisi.** Si risolve la struttura con i moduli `x^p E₀` e si calcola la
   deformabilità *c = Fᵀu* (somma pesata sui casi, con più casi di carico).
2. **Numero di sensibilità** di ogni elemento, per unità di volume:

   *αₑ = ½ xₑ^(p−1) uₑᵀ kₑ uₑ / vₑ*

   dove *kₑ* è la rigidezza dell'elemento pieno e *vₑ* il suo volume (area per
   spessore nei gusci). Per un elemento pieno è la sua energia di
   deformazione; per un vuoto è la stessa quantità ridotta di *x_min^(p−1) = 10⁻⁶*.
3. **Filtro** a cono di raggio *r*: *α̃ₑ = Σⱼ wₑⱼ αⱼ / Σⱼ wₑⱼ*, con
   *wₑⱼ = r − dₑⱼ* per gli elementi a distanza *dₑⱼ < r*. Il filtro spalma la
   sensibilità sui vicini: un vuoto accanto a elementi molto sollecitati riceve
   un valore alto e può rientrare, e si evitano le scacchiere.
4. **Media storica**: *α̃ₑ⁽ᵏ⁾ ← (α̃ₑ⁽ᵏ⁾ + α̃ₑ⁽ᵏ⁻¹⁾) / 2*. Smorza le oscillazioni
   fra un'iterazione e l'altra (Huang e Xie, 2007).
5. **Volume del passo**: *V₍ₖ₊₁₎ = max(V\*, Vₖ (1 − er))*, con *V\** il volume
   obiettivo ed *er* il rapporto evolutivo (default 2 %).
6. **Soglia**: restano pieni gli elementi con *α̃ₑ > α_th*, con *α_th* trovata per
   bisezione in modo che il volume dei pieni sia *V₍ₖ₊₁₎*; gli altri diventano
   vuoti. Elementi passivi e caricati restano pieni, quelli imposti vuoti restano
   vuoti. Le sensibilità si arrotondano a 8 cifre rispetto alla mediana, così due
   elementi simmetrici cadono dalla stessa parte della soglia e il disegno resta
   simmetrico.
7. **Controllo di connessione** (in più del metodo originale, vedi sotto).
8. **Arresto**: raggiunto *V\**, quando la deformabilità media delle ultime
   cinque iterazioni differisce da quella delle cinque precedenti meno di *τ*:

   *|Σᵢ₌₀..₄ c₍ₖ₋ᵢ₎ − Σᵢ₌₅..₉ c₍ₖ₋ᵢ₎| / Σᵢ₌₀..₄ c₍ₖ₋ᵢ₎ ≤ τ*, con *τ = 10⁻³*.

**Il controllo di connessione.** Con un carico concentrato e frazioni basse, il
BESO di Huang e Xie può togliere in un solo passo l'elemento che tiene attaccato
il carico. La deformabilità sale allora di otto o nove ordini di grandezza (il
carico è portato solo dai vuoti) e il disegno non si riprende più: le
sensibilità vicino al distacco diventano enormi e la soglia rimette materiale
dove capita. Lo fa anche una trascrizione fedele del loro codice, ed è il caso
della figura 3. Dopo ogni passo si controlla quindi che ogni nodo caricato
arrivi ai vincoli attraverso elementi pieni che si toccano per **un lato** (gusci)
o per **una faccia** (solidi). Un solo vertice o un solo spigolo in comune è una
cerniera e non conta. Contano come ancoraggi i nodi con una traslazione
bloccata, salvo una direzione bloccata su tutti i nodi (come `uz` in un problema
piano), e i nodi di ciò che non è di progetto (travi, molle, vincoli cinematici).
Se il passo stacca il carico, si rimette l'insieme minimo degli elementi tolti
che lo riattacca, preferendo i più sensibili e tenendo insieme quelli con la
stessa sensibilità. Il volume del passo resta un po' sopra l'obiettivo e i passi
seguenti tolgono altrove. Su mesh rade può capitare che il volume non scenda
fino alla frazione chiesta: un avviso lo dice. Si spegne con `connessione=False`.

## Quale metodo scegliere

| | SIMP | BESO |
|---|---|---|
| Densità | continue fra 0 e 1, con zone grigie | solo pieno o vuoto |
| Partenza | densità uniforme pari alla frazione | struttura piena, il volume cala del 2 % per passo |
| Iterazioni negli esempi di questa pagina | da 40 (lastra) a 263 (trave MBB fitta) | 81-86; con *er* = 2 % servono circa 60 passi solo per scendere al 30 % |
| Modello ridotto | richiede una soglia: quella che conserva il volume, abbassata se il taglio lascia cerniere | è il disegno stesso (soglia 0,5) |
| Mesh rade | disegno molto grigio, modello ridotto spesso labile | disegno netto, ma il volume può fermarsi sopra l'obiettivo |
| Quando usarlo | prima esplorazione, mesh qualunque, più casi di carico | disegno da trasformare subito in geometria, confronto diretto fra varianti |

In pratica: SIMP per vedere dove va il materiale, BESO quando serve un disegno
netto da rianalizzare, verificare o disegnare. Sulla lastra dell'esempio guidato
i due metodi danno lo stesso schema, a briglie convergenti con una diagonale
centrale.

## Uso

```python
from feagent.topology import ottimizza, figura, centro

r = ottimizza(model, frazione=0.3, metodo="simp")      # oppure "beso"
print(r.tabella())                                    # sintesi
figura(r, "disegno.png")                              # densità in scala di grigi
m2 = r.modello_ridotto()                              # senza gli elementi tolti
res = m2.solve(sparse=True)                           # verifica del disegno
```

Parametri di `ottimizza` (tutti per nome dopo `frazione` e `metodo`):

| Parametro | Default | Significato |
|---|---|---|
| `frazione` | 0,3 | frazione del volume di progetto da conservare (0 < frazione < 1); il volume di progetto esclude i passivi e comprende i vuoti imposti |
| `metodo` | `"simp"` | `"simp"` o `"beso"` |
| `tipi` | tutti | elementi che possono perdere materiale: `"solid"`, `"shell"`, `"shell_tri"` |
| `passivi`, `vuoti` | nessuno | elementi sempre pieni o sempre vuoti: elenco di id (solidi), di `(tipo, id)` oppure funzione `f(tipo, id, elemento)`; `centro(elemento)` dà il baricentro |
| `casi`, `pesi` | caso di default, pesi 1 | caso o combinazione come in `Model.solve` (`{"Q": 1.0}`), oppure elenco di combinazioni: l'obiettivo è la somma pesata delle deformabilità |
| `carichi_pieni` | sì | gli elementi caricati (pressioni, carichi di superficie, nodi caricati) restano pieni |
| `raggio` | 1,5 *h* | raggio del filtro, con *h* la dimensione mediana degli elementi; più grande dà membrature più spesse e meno numerose |
| `penal` | 3 | esponente di penalizzazione *p* (SIMP e BESO) |
| `iterazioni` | 200 | limite di iterazioni |
| `tol` | SIMP 0,01; BESO 10⁻³ | arresto: SIMP massima variazione di densità, BESO variazione relativa della deformabilità sulle ultime dieci iterazioni |
| `mossa` | 0,2 | SIMP: variazione massima della densità per iterazione |
| `er` | 0,02 | BESO: rapporto evolutivo, quanto volume si toglie per passo |
| `connessione` | sì | BESO: nessun passo stacca i nodi caricati dai vincoli |
| `xmin` | 10⁻³ | BESO: densità degli elementi vuoti |
| `Emin` | 10⁻⁹ | SIMP: modulo relativo del vuoto |
| `verbose` | nessuna | funzione `f(iterazione, deformabilità, volume, cambio)` chiamata a ogni iterazione |

Il risultato (`RisultatoTopologia`) espone `rho` (densità per elemento, nello
stesso ordine di `elementi`), `densita()` (dizionario `(tipo, id) → ρ`),
`deformabilita` e `volume` (storia per iterazione), `iterazioni`,
`convergenza`, `frazione_ottenuta(soglia)`, `grigio()` (volume con
0,1 < ρ < 0,9), `soglia_per_volume(frazione)`, `pieni(soglia)`, `tabella()`,
`modello_ridotto(soglia)` e, dopo quest'ultimo, `soglia_ridotto` (la soglia
usata davvero).

Travi, molle, bielle, vincoli elastici e vincoli cinematici (link rigidi,
equalDOF, diaframmi) restano nel modello con la loro rigidezza e non perdono
materiale. I cavi non sono ammessi, perché richiedono l'analisi non lineare.

**Ipotesi.** Materiale elastico lineare. Carichi indipendenti dalla densità: il
peso proprio degli elementi di progetto resta come carico fisso, e il modulo lo
segnala con un avviso. Vincoli a spostamento nullo, senza cedimenti imposti.

## Esempio guidato: lastra a mensola

Lastra d'acciaio 3 m × 1 m, spessore 10 mm, 48 × 16 gusci Q4 nel piano XY,
incastrata sul lato x = 0 e caricata da 10 kN verso il basso nel mezzo del lato
libero. Si conserva il 30 % del volume. Lo script completo, che produce anche
le figure, è `examples/ex21_topologia_lastra.py`.

```python
from feagent import Material, Model, ShellSection
from feagent.topology import figura, ottimizza

m = Model()
acciaio, sec = Material(E=210e9, nu=0.3, name="S355"), ShellSection(t=0.01)
nx, ny = 48, 16
nid = lambda i, j: i * (ny + 1) + j + 1
for i in range(nx + 1):
    for j in range(ny + 1):
        m.add_node(nid(i, j), 3.0 * i / nx, 1.0 * j / ny, 0.0)
e = 1
for i in range(nx):
    for j in range(ny):
        m.add_shell(e, [nid(i, j), nid(i + 1, j), nid(i + 1, j + 1), nid(i, j + 1)], acciaio, sec)
        e += 1
for j in range(ny + 1):
    m.fix(nid(0, j))                                   # incastro
m.add_nodal_load(nid(nx, ny // 2), case="Q", Fy=-1e4)

r = ottimizza(m, frazione=0.3, metodo="beso", tipi=("shell", "shell_tri"),
              casi={"Q": 1.0}, iterazioni=100)
print(r.tabella())
figura(r, "lastra_beso.png")
m2 = r.modello_ridotto()                               # 230 gusci
res = m2.solve(cases={"Q": 1.0}, sparse=True)
print(m2.assemble_loads({"Q": 1.0}) @ res.U)           # deformabilità del ridotto
```

Tre cose da notare nel modello: i gusci sono nel piano XY e il carico nel piano,
quindi lavorano a membrana; i GdL fuori piano restano liberi e i modi
flessionali della lastra sottile non disturbano (il modello ridotto li distingue
dai meccanismi veri); il nodo caricato appartiene a due gusci, che restano pieni.

| | SIMP | BESO | BESO senza connessione |
|---|---|---|---|
| Iterazioni | 40 | 86 | 100 (non converge) |
| Deformabilità iniziale [J] | 205,6 | 5,60 | 5,60 |
| Deformabilità finale [J] | 23,23 (con il grigio) | 16,88 | 3,9 · 10⁸ |
| Frazione con soglia 0,5 | 0,298 | 0,298 | 0,299 |
| Volume in grigio | 0,495 | 0 | 0 |
| Modello ridotto | 263 gusci, soglia abbassata a 0,419 (frazione 0,34) | 230 gusci, soglia 0,5 | non disponibile: carico staccato |
| Deformabilità del ridotto [J] | 12,31 | 16,62 | |

Come leggere i numeri:

* SIMP parte dalla densità uniforme 0,3, quindi da una struttura molto
  deformabile (205,6); BESO parte dalla lastra piena (5,60) e si ammorbidisce
  man mano che toglie materiale.
* La deformabilità finale di SIMP comprende le densità intermedie penalizzate e
  non si confronta direttamente con BESO. Il confronto giusto è fra i modelli
  ridotti, dove ogni guscio è pieno o tolto.
* Il ridotto SIMP tagliato alla soglia che conserva il volume lasciava catene di
  gusci uniti per un vertice (meccanismi fuori piano); la soglia si abbassa
  finché spariscono, e il modello ha il 34 % del volume: per questo è più rigido
  del BESO al 30 %.
* Senza il controllo di connessione BESO stacca il carico all'iterazione 52 e
  non si riprende (figure 3 e 4).

![Lastra SIMP](images/topologia/lastra_simp.png)
*Figura 1 — Lastra a mensola, SIMP, frazione 0,3: densità (nero = pieno, grigio = densità intermedia).*

![Lastra BESO](images/topologia/lastra_beso.png)
*Figura 2 — Lastra a mensola, BESO, frazione 0,3: briglie convergenti verso il carico e diagonale centrale.*

![Lastra BESO senza connessione](images/topologia/lastra_beso_senza_connessione.png)
*Figura 3 — BESO senza controllo di connessione: il carico resta appeso ai soli vuoti.*

![Storia](images/topologia/lastra_storia.png)
*Figura 4 — Deformabilità (scala logaritmica) e frazione di volume per iterazione. Il picco di BESO all'iterazione 52 è un distacco riparato dal controllo di connessione; senza, la deformabilità sale a 10⁹ e ci resta.*

## Dall'interfaccia desktop

L'ottimizzazione lavora sui gusci del modello aperto (servono gusci o piastre).
Per rifare l'esempio guidato:

1. **Modello.** Si costruisce la lastra con **Modello > Genera piastra a maglia…** (o si
   apre un modello con i gusci), si incastra il lato sinistro e si mette la forza
   nel caso `Q`.
2. **Analisi > Ottimizzazione topologica (SIMP/BESO)…** apre il dialogo (figura 5):
   metodo, frazione di volume, iterazioni massime, raggio del filtro (vuoto =
   automatico, 1,5 volte la dimensione dei gusci) e casi con i coefficienti
   (`Q=1`, oppure `G=1 Q=1.5`).
3. **Esegui.** Il risultato è la mappa delle densità (figura 6): ogni guscio ha
   il colore della sua densità, senza medie sui nodi, quindi BESO mostra solo
   pieno e vuoto e SIMP le densità intermedie vere. Il registro riporta la
   tabella di sintesi e le note: iterazioni, frazione ottenuta con la soglia,
   gusci del modello ridotto ed eventuali avvisi (soglia abbassata, volume
   rimasto sopra l'obiettivo).
4. **Risultati > Storia dell'ottimizzazione** mostra deformabilità e frazione di
   volume per iterazione (figura 7).
5. **Risultati > Apri il modello ridotto** apre il modello senza i gusci tolti
   (figura 8), con lo stesso asse verticale del modello di partenza e senza i
   nodi rimasti senza gusci. Si analizza, si verifica e si salva come qualunque
   altro modello.

![Dialogo](images/gui_topology_dialog.png)
*Figura 5 — Il dialogo dell'ottimizzazione topologica, con BESO e frazione 0,3.*

![Densità BESO](images/gui_topology_beso.png)
*Figura 6 — Densità BESO della lastra: gusci pieni (arancio) o vuoti (viola).*

![Storia nella GUI](images/gui_topology_history.png)
*Figura 7 — Storia dell'ottimizzazione nell'interfaccia.*

![Modello ridotto](images/gui_topology_reduced.png)
*Figura 8 — Il modello ridotto riaperto e rianalizzato: 230 gusci, deformata sotto il carico Q.*

## Messaggi e cosa fare

| Messaggio | Che cosa vuol dire | Cosa fare |
|---|---|---|
| `BESO: frazione di volume ferma a … (chiesta …) per non staccare il carico dai vincoli` | sulla mesh data BESO non trova un disegno collegato alla frazione chiesta | infittire la mesh (una quarantina di elementi sul lato lungo), alzare la frazione o usare SIMP |
| `modello ridotto SIMP: soglia abbassata da … a …` | il taglio del grigio lasciava cerniere nel percorso del carico | nulla: il ridotto è valido, con il volume indicato; per tornare al volume chiesto infittire la mesh o usare BESO |
| `il modello ridotto è labile anche togliendo le parti appese` | anche abbassando la soglia restano meccanismi che toccano elementi da tenere pieni | mesh più fitta; `modello_ridotto(soglia)` con una soglia più bassa; BESO |
| `con la soglia … elementi da tenere pieni restano staccati dai vincoli` | la soglia taglia il percorso del carico | soglia più bassa o mesh più fitta |
| `nodi caricati restano senza elementi` | con `carichi_pieni=False` sono stati tolti gli elementi attorno al carico | lasciare `carichi_pieni=True` o abbassare la soglia |
| deformabilità finale molto più grande di quella iniziale (nota dell'interfaccia) | la struttura è staccata dal carico o dai vincoli | controllare vincoli e carichi; frazione più alta o mesh più fitta |
| `forze di volume … trattate come carico fisso` | il peso proprio degli elementi di progetto non cala con il materiale tolto | accettabile se il peso è piccolo rispetto ai carichi; altrimenti ottimizzare senza peso proprio e verificare il ridotto con il peso |

## Il modello ridotto

`modello_ridotto(soglia)` è una copia del modello in cui:

* si tolgono gli elementi con densità sotto la soglia. Il default con SIMP è la
  soglia che conserva il volume chiesto; con BESO è 0,5;
* si tolgono le **isole**, cioè le parti che nessun percorso di nodi comuni,
  travi o vincoli collega ai vincoli;
* si tolgono le **parti appese per uno spigolo o un vertice**. Fra esaedri sono
  cerniere che renderebbero labile il modello. Fra gusci lo è anche un solo
  vertice in comune (il drilling non è legato alle traslazioni). Si trovano come
  moti a energia nulla della rigidezza del modello ridotto, scalata con la sua
  diagonale. Senza la scalatura, in una lastra sottile i modi flessionali fuori
  piano passavano per meccanismi e la ricerca degli autovalori non convergeva;
  ora il numero di modi cercati cresce finché i moti nulli sono presi tutti;
* con SIMP e la soglia di default, il taglio del grigio può lasciare catene di
  elementi uniti per un vertice in pieno percorso del carico. Toglierle lascia un
  modello molto più deformabile (lastra a mensola 48 × 16, frazione 0,3: 305
  invece di 12). Si prova allora ad abbassare la soglia, fino a +0,10 di volume,
  e si tiene il modello con la minore deformabilità per volume. Un avviso dice
  la soglia usata, che resta in `soglia_ridotto`;
* si tolgono le forze di volume e i carichi termici degli elementi rimossi, e i
  nodi rimasti senza elementi si bloccano (nell'interfaccia si tolgono del tutto,
  con i loro vincoli, così il modello ridotto non mostra centinaia di vincoli
  inutili).

Se un elemento da tenere pieno (caricato o passivo) resta staccato dai vincoli,
la soglia taglia il percorso del carico: il modulo si ferma con un errore
esplicito e non restituisce un modello che manda il carico direttamente ai
vincoli.

## Solutore

A ogni iterazione si risolve il sistema lineare. Con i solidi la fattorizzazione
sparsa è la parte più costosa, ma le densità cambiano poco da un'iterazione
all'altra. Si usa quindi il gradiente coniugato precondizionato con la
fattorizzazione di un'iterazione precedente (rianalisi approssimata, Amir,
Bendsøe e Sigmund, IJNME 79, 2009), con tolleranza relativa 10⁻¹⁰. Si
rifattorizza quando servono più di 20 iterazioni. Sulla trave MBB la storia
della deformabilità coincide con quella della soluzione diretta.

## Validazione

**Trave MBB contro il codice a 88 righe.** Metà trave appoggiata, 30 × 10
gusci Q4 nel piano (la membrana di feagent è il quadrilatero bilineare di top88),
frazione 0,5, *p = 3*, *r = 1,5*. Il confronto è con una trascrizione
indipendente di top88 nel test: storia della deformabilità coincidente entro
10⁻⁸ relativo, densità entro 10⁻⁶, stesse iterazioni. A 60 × 20 la coincidenza
è la stessa: 126 iterazioni, deformabilità 1007,02 → 218,80.

![MBB](images/topologia/mbb_simp.png)
*Figura 9 — Trave MBB 120 × 40, SIMP, frazione 0,5: 263 iterazioni, deformabilità 1026,8 → 201,2.*

**Mensola 3D a esaedri.** 24 × 6 × 12 esaedri, incastro su una faccia, carico
in punta, frazione 0,25. Il disegno è simmetrico rispetto ai due piani medi e il
modello ridotto è in equilibrio.

| Metodo | Iterazioni | Deformabilità dell'ottimizzazione | Esaedri del modello ridotto | Deformabilità del modello ridotto |
|---|---|---|---|---|
| SIMP | 77 | 137,19 | 451 su 1728 | 80,63 |
| BESO | 81 | 79,76 | 448 su 1728 | 79,84 |

Con BESO il modello ridotto è lo stesso disegno: le deformabilità coincidono
entro lo 0,1 %. Con SIMP la deformabilità dell'ottimizzazione comprende le
densità intermedie penalizzate. Nel modello ridotto gli elementi sopra soglia
diventano pieni, quindi il modello ridotto è più rigido del disegno grigio. I
due metodi arrivano allo stesso schema, a briglie e diagonali.

![Mensola SIMP](images/topologia/mensola_simp.png)
*Figura 10 — Mensola 3D, SIMP, elementi sopra la soglia che conserva il volume (grigio = densità intermedia).*

![Mensola BESO](images/topologia/mensola_beso.png)
*Figura 11 — Mensola 3D, BESO: elementi pieni o vuoti.*

**Trave parete.** Appoggi agli estremi, carico distribuito sul bordo
superiore. Il bordo superiore e le zone d'appoggio restano pieni. Ne esce
l'arco con tirante, lo schema tirante-puntone della trave tozza.

![Trave parete](images/topologia/trave_parete_simp.png)
*Figura 12 — Trave parete 4 × 2 m, 96 × 48 gusci, SIMP, frazione 0,3: arco e tirante.*

Gli altri test (`tests/test_topology.py`) controllano:

* il rispetto di elementi passivi e vuoti;
* che due casi di carico simmetrici diano un disegno simmetrico, e il solo caso
  A no;
* che il carico portato da un equalDOF dia lo stesso disegno del carico diretto;
* che la parte appesa per uno spigolo sia tolta dal modello ridotto;
* che BESO su una lastra a mensola 48 × 16 gusci con carico concentrato, frazione
  0,3, resti attaccato al carico: disegno simmetrico, deformabilità 16,9 contro
  23,2 di SIMP, modello ridotto in equilibrio (prima: 3,9·10⁸ dopo 100 iterazioni);
  sulla mesh 24 × 8 il disegno è simmetrico e un avviso segnala il volume rimasto
  sopra la frazione chiesta;
* che SIMP sulla stessa lastra dia un modello ridotto risolvibile e in equilibrio
  in pochi secondi: a 0,3 con la soglia abbassata (prima 137 s e matrice singolare),
  a 0,4 senza (prima un falso meccanismo fuori piano lo rendeva non disponibile);
  e che una lastra intera da 5 mm non risulti labile;
* gli errori sui parametri.

Script delle figure: `examples/ex20_topologia.py` (validazione), `examples/ex21_topologia_lastra.py` (esempio guidato) e `scripts/_gen_gui_topology_screenshots.py` (schermate dell'interfaccia).
