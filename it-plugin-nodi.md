# Plugin `nodi`: nodi in acciaio con il metodo CBFEM

Stato: **fase 2** (primi nodi). Il motore ha tutti gli ingredienti del metodo, validati uno per uno:
guscio elastoplastico, criterio del 5 % indipendente dalla mesh, piastre con i fori meshate
separatamente, cordoni d'angolo, bulloni a trazione e a taglio con rifollamento, contatto con un piano
rigido e fra piastre deformabili, sezioni rigide per introdurre le azioni, controllo a forze. Nodi
disponibili: il T-stub bullonato, il **giunto trave-trave a coprigiunti** dei conci di travi da ponte e
qualunque nodo **importato da CAD** (STEP, IFC).

## Il metodo

Le piastre del nodo sono gusci `ShellQ4` con plasticita' alla von Mises (incrudimento lineare, modulo
tangente E/1000) integrata nello spessore con Simpson su 9 punti: le fibre estreme sono punti di
integrazione, quindi la deformazione plastica si legge in superficie, e momento plastico e rigidezza
elastica sono integrati esattamente. Il taglio trasversale resta elastico. Lo stato limite e' il 5 % di
deformazione plastica equivalente (EN 1993-1-5, Annex C), come nel metodo CBFEM di letteratura (Wald,
Sabatka e altri, 2016).

**Deformazione di controllo.** Il massimo nel punto di integrazione dipende dalla mesh, perche' la
deformazione si localizza. La deformazione di controllo e' quindi la media, pesata sulle aree, degli
elementi della stessa piastra entro una distanza pari allo spessore: e' l'estensione di una cerniera
plastica in una piastra, e rende il criterio indipendente dalla dimensione degli elementi (tabella sotto).

**Piastre.** Ogni piastra e' un poligono nel proprio piano con i fori e si mesha da sola a quadrilateri
con Gmsh (`piastre.py`). Le piastre non devono avere mesh congruenti: i connettori collegano un nodo di
una piastra a un punto interno a un elemento dell'altra. E' la costruzione che serve sia ai nodi
parametrici sia alla geometria importata da CAD.

**Connettori** (`feagent/shell_nl_connettori.py`):

* **contatto** su piano rigido e fra piastre (nodo-superficie), solo a compressione, senza attrito;
* **bullone a trazione**: molla solo a trazione, elastica perfettamente plastica a `Ft = 0,9 fub As`,
  rigidezza `E As / Lb`, fra le corone circolari sotto la testa e sotto il dado (come un RBE3);
* **bullone a taglio**: il gambo spinge sul bordo del foro con molle radiali solo a compressione
  (rifollamento, con il gioco foro-bullone se richiesto); fra i piani di taglio lavora una molla
  elastica perfettamente plastica alla resistenza a taglio;
* **cordone d'angolo**: collega la fibra della piastra A sul lato del cordone alla superficie della
  piastra B, con le tensioni della sezione di gola `sigma_perp`, `tau_perp`, `tau_par` e il limite
  `sqrt(sigma_perp^2 + 3 (tau_perp^2 + tau_par^2)) <= fu / beta_w` (EN 1993-1-8 §4.5.3.2). E' elastico
  perfettamente plastico, quindi i picchi (per esempio alle estremita') si ridistribuiscono; la
  deformazione plastica del cordone e' lo scorrimento plastico diviso per la gola.

**Soluzione**: Newton a spostamento imposto, predittore lineare (senza, il passo imposto si concentra in
una fila di elementi e il Newton diverge), dimezzamento del passo se non converge.

## Uso

```python
from feagent.plugins import get
r = get("nodi").run("tstub", tf=20, d=20, classe="10.9")
print(r.message)

from feagent.plugins.nodi.tstub import TStub, analizza_piastre
out = analizza_piastre(TStub(tf=15), doppio=True)     # due T-stub affacciati, fori, cordoni, contatto
```

```bash
feagent plugin run nodi tstub --param tf=20 --param h=10
```

## Validazione

Prove con soluzione chiusa (in `tests/test_shell_nl.py`):

| Prova | Riferimento | Esito |
|---|---|---|
| Tangente consistente J2 in tensione piana | differenze finite | scarto 1e-9 |
| Mensola elastica | 3 E I / L^3 | 0,999 |
| Trave in deformazione piana, collasso | 4 (2/√3) Mp / L con il momento al centro dell'elemento | 1,0000 |
| Cordoni d'angolo tirati lungo il cordone | 2 a L fw / √3 | 1,0000 |
| Cordoni d'angolo tirati trasversalmente | √2 a L fw | 1,0000 |
| Giunto a sovrapposizione con un bullone | resistenza a taglio del gambo | 1,0000 |
| Due T-stub affacciati con contatto fra le ali | T-stub su piano rigido (simmetria) | 205,5 contro 205,5 kN |

**Indipendenza dalla mesh** (T-stub a griglia):

| passo della mesh | 10 mm | 5 mm | 2,5 mm |
|---|---|---|---|
| ala 15 mm, massimo nel punto di integrazione | 230,6 kN | 215,7 kN | 196,8 kN |
| ala 15 mm, media su una distanza pari allo spessore | 233,2 kN | 223,7 kN | 220,9 kN |
| ala 10 mm, media su una distanza pari allo spessore | 113,3 kN | 106,4 kN | 103,5 kN |

**T-stub contro EN 1993-1-8** (Tab. 6.2, coefficienti parziali unitari). T-stub 200 x 120 mm, anima
10 mm con cordoni di gola 7 mm, due M20 10.9 a interasse 120 mm, S355, mesh 5 mm, modello a piastre
con i fori; m = 47,1 mm dal bullone al piede del cordone meno 0,8 a √2:

| tf [mm] | CBFEM [kN] | EN 1993-1-8 [kN] | rapporto |
|---|---|---|---|
| 10 (due T-stub affacciati) | 98,2 | 90,5 (modo 1, metodo 1); 106,0 (metodo 2) | 1,08; 0,93 |
| 15 | 205,5 | 203,6 (modo 1, metodo 1) | 1,01 |

Il modello a griglia della fase 0 (ala e anima con nodi in comune, senza fori ne' cordoni) resta come
riferimento rapido: con l'anima sul piano medio e senza fori e' piu' rigido e piu' resistente (da 1,03 a
1,15 sopra la norma).

## Giunto trave-trave a coprigiunti (conci di travi da ponte)

Azione `coprigiunto` (modulo `coprigiunto.py`). Geometria e regole dell'app GiuntoISection:

* coprigiunto d'ala rettangolare o **sagomato**, rastremato alle estremita' (larghezza `b` al centro,
  `b_end` alle estremita' sul tratto `L_taper`); tutte le piastre del giunto di piattabanda (esterna,
  interne, riempimenti) hanno **la stessa sagoma**, ritagliata con le fasce ai lati dell'anima;
* piastre interne spezzate ai lati dell'anima; piastra esterna intera o **spezzata** (`esterno_diviso`:
  di norma spezzata sull'ala superiore, dove ci sono i pioli, intera sull'inferiore);
* bulloni d'ala allineati o **a quinconce** (file trasversali alterne sfalsate di mezzo passo), centrati
  nelle mezze piastre quando sono spezzate; restano i soli bulloni il cui foro sta dentro tutte le piastre
  della sua pila;
* coprigiunto d'anima doppio, prima fila dei bulloni a `e1` dall'asse del giunto;
* piattabande dei due conci di spessore diverso: facce esterne allineate e piastre di riempimento sul
  lato piu' sottile, fra piattabanda e piastre interne.

Ogni bullone attraversa la sua pila di piastre: taglio su ogni piano (rigidezza `d^2 fub` della
Tab. 6.11), rifollamento su ogni foro, trazione fra testa e dado. Le piastre affiancate sono in contatto,
l'anima e' legata alle ali da cordoni elastici. N, V, M nella sezione del giunto si introducono con
sezioni rigide alle estremita' dei conci (lunghezza del concio: meta' del coprigiunto piu' lungo + H/2) e
il giunto si verifica alle azioni di progetto; con `lam_max > 1` l'analisi prosegue fino al collasso.

Verifiche EN 1993-1-8 sulle forze del modello: taglio per piano (con `beta_Lf` dei giunti lunghi, §3.8),
trazione, interazione (Tab. 3.4), rifollamento su ogni piastra della pila, distanze minime dei fori
(Tab. 3.3), deformazione plastica delle piastre entro il 5 %. Distanze e interassi (e1, e2, p1, p2) si
misurano sulla geometria reale di ogni piastra, nella direzione della forza di rifollamento del modello e in
quella ortogonale: con i coprigiunti sagomati i fori d'angolo possono finire vicini al bordo rastremato, e
le distanze dichiarate non bastano a vederlo. I bulloni non si tolgono dal progetto: si esclude solo un foro
che uscirebbe dalla piastra. Il risultato confronta le forze sui bulloni con la ripartizione elastica classica
di GiuntoISection (parziale ripristino).

**Esempio.** Trave H 1800 mm, piattabanda superiore 500 x 30, inferiore 700 x 40, anima 18. Giunto di
piattabanda superiore sagomato (500 -> 420 mm su 100 mm), piastra esterna spezzata, 4 x 4 bulloni M24 10.9
per lato; inferiore sagomato (700 -> 420 mm su 200 mm), piastra esterna intera, 6 x 5 bulloni a quinconce
per lato; anima 2 x 12 mm con 3 x 10 bulloni per lato; M = 6000 kNm, V = 1500 kN. Modello di 36 mila
gusci e 148 bulloni, 6 minuti con Pardiso. Esito: verificato; D/C massimo 0,78 (distanze minime dei fori
d'angolo), interazione taglio-trazione 0,55 sull'ala superiore, deformazione plastica 0,02 %.

| Gruppo | modello, media per piano | modello, massimo | ripartizione classica |
|---|---|---|---|
| ala superiore | 83,2 kN | 91,2 kN | 73,8 kN |
| ala inferiore | 59,4 kN | 69,0 kN | 55,3 kN |
| anima | 28,6 kN | 51,3 kN | 88,4 kN |

Nel modello le ali portano piu' di quanto dice la ripartizione elastica sulla sezione lorda (dall'8 al
13 %) e il coprigiunto d'anima molto meno: l'anima giuntata e' piu' deformabile della sezione continua,
per i giochi di rifollamento e la flessibilita' dei coprigiunti, e il momento migra verso le ali. La
ripartizione classica e' quindi a favore di sicurezza sull'anima e non sulle ali.

![Giunto trave-trave, deformata](../img/nodi/giunto_deformata.png)
![Coprigiunto superiore sagomato e spezzato](../img/nodi/giunto_coprigiunto_superiore.png)
![Coprigiunto inferiore sagomato con bulloni a quinconce](../img/nodi/giunto_coprigiunto_inferiore.png)
![Coprigiunto d'anima](../img/nodi/giunto_coprigiunto_anima.png)

**Relazione di calcolo Word.** Con il parametro `relazione` (attivo di default) l'azione scrive
`relazione_giunto_trave_trave.docx` nella cartella delle uscite, sovrascrivendola a ogni calcolo:
premessa, normativa, materiali, geometria dei conci e dei giunti di piattabanda e d'anima con le piante,
azioni, modello di calcolo, risultati con le figure, verifiche dei bulloni con le formule di
EN 1993-1-8 in equazioni native di Word, confronto con la ripartizione elastica, conclusioni con il
riepilogo delle verifiche (D/C ed esito) e, in allegato, forze e D/C di ogni bullone. Stile aziendale
(Calibri 11, titoli neri, tabelle native con intestazione D9E1F2, didascalie "Figura N"), nessun nome
di software nel testo, ogni numero dal calcolo.

**Prestazioni.** Il solutore lineare Pardiso (pacchetto `pypardiso`, licenza BSD-3; MKL con licenza
Intel Simplified Software License, ridistribuibile) e' usato se installato; altrimenti SuperLU, circa 5
volte piu' lento sul giunto di prova e impraticabile sui giunti da ponte.

## Nodi importati da CAD (STEP, IFC)

Azioni `cad_importa` e `cad_verifica` (modulo `cad.py`). Il percorso e' uguale per i due formati:

1. ogni parte solida diventa un insieme di facce piane: dallo STEP si leggono le facce esatte della B-rep
   (con OpenCascade, anche quelle piane descritte come B-spline), dall'IFC si raggruppano i triangoli
   complanari della geometria;
2. in ogni parte le coppie di facce piane parallele e affacciate sono piastre: superficie media, spessore,
   contorno, fori circolari, aperture di forma qualunque. Una lamiera da' una piastra, un profilo a doppio T
   tre (ali e anima). Bulloni, dadi e rondelle non danno piastre e si ignorano: i bulloni si ricostruiscono
   dai fori;
3. collegamenti dalla geometria: bordo di una piastra sulla faccia di un'altra dello stesso profilo, unione
   rigida; fra parti diverse, cordoni d'angolo su entrambi i lati (gola a scelta, di default 0,7 volte lo
   spessore); piastre affiancate faccia contro faccia, contatto; fori coassiali attraverso piastre a
   contatto, un bullone (diametro dal foro normale);
4. le teste delle aste lontane dal nodo diventano sezioni rigide con il nodo master nel baricentro della
   sezione; `cad_importa` ne elenca i nomi, `cad_verifica` riceve per ciascuna `{"vincolo": "incastro"}`
   oppure le azioni (Fx, Fy, Fz in kN; Mx, My, Mz in kNm, assi globali).

Le verifiche sono quelle del giunto trave-trave (bulloni, rifollamento con le distanze misurate, distanze
minime, deformazione plastica) piu' i cordoni di saldatura (utilizzo e deformazione plastica).

**Validazione.** Il giunto parametrico di prova (trave H 600, 17 piastre, 60 bulloni, piattabande di
spessore diverso con riempimenti, coprigiunti sagomati) esportato in STEP con i conci come solidi unici e
reimportato: le 17 piastre tornano con spessori, sagome e fori; i conci si scompongono in ali e anima con le
quattro unioni; tornano i 60 bulloni (8 attraverso il riempimento) e le due teste dei conci. Con le stesse
azioni le forze sui bulloni coincidono con il modello parametrico:

| Gruppo | parametrico, media / massimo | da STEP, media / massimo |
|---|---|---|
| ala superiore | 26,6 / 28,1 kN | 26,4 / 27,7 kN |
| ala inferiore | 22,3 / 25,2 kN | 22,3 / 24,5 kN |
| anima | 11,9 / 26,7 kN | 12,5 / 28,0 kN |

Lo stesso confronto ha fatto emergere che in quel giunto i fori d'angolo sono a 15 mm dal bordo rastremato
(minimo 1,2 d0 = 26 mm): da li' la verifica delle distanze misurate sulla geometria anche nel parametrico.

**Limiti dell'import.** Le parti curve (tubi, raccordi dei profili laminati) non diventano piastre: i
raccordi si perdono, i tubi non sono ancora trattati; un profilo deve avere le facce delle ali e dell'anima
piane. Le saldature si deducono dal contatto bordo-faccia fra parti diverse: un appoggio non saldato va
tolto a mano. La classe dei bulloni e' unica per il nodo.

## Limiti attuali

* piccoli spostamenti, nessuna analisi di instabilita';
* nessun attrito nel contatto, niente precarico dei bulloni: i giunti ad attrito (categorie B e C) si
  verificano sulle forze del modello, ma il modello lavora a taglio e rifollamento;
* le verifiche delle piastre forate (sezione netta, block tearing) sono affidate alla plasticita' del
  modello (criterio del 5 %), non alle formule di EN 1993-1-8.

## Prossime fasi

2. Nodi parametrici: dopo T-stub e giunto trave-trave, piastra d'estremita' trave-colonna, piastra di
   base, fin plate, fazzoletto; relazione Word.
3. Import: tubi e profili con raccordi, classe e precarico dei bulloni da `IfcMechanicalFastener`,
   relazione Word anche per i nodi importati.
4. Instabilita' (LBA), curva M-φ e classificazione di rigidezza, piastre di base con ancoraggi.

## Figure

Ala da 10 mm (modo 1): cerniere plastiche al piede dell'anima e alla fila dei bulloni, che si piegano
attorno all'impronta della testa.

![Deformata, ala 10 mm](../img/nodi/tstub_deformata_tf10.png)
![Ala 10 mm, deformazione plastica](../img/nodi/tstub_ala_tf10.png)
![Curve carico-spostamento](../img/nodi/tstub_curve.png)

Modello a piastre: due T-stub affacciati, fori, cordoni e contatto fra le ali.

![Due T-stub affacciati](../img/nodi/tstub_doppio_deformata_tf10.png)
![Ala superiore con i fori](../img/nodi/tstub_doppio_ala_tf10.png)
