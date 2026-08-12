---
layout: default
title: "36 - Ponte sullo Stretto di Messina"
parent: "14 - Esempi d'Uso"
grand_parent: Italiano
nav_order: 11
---

# 36 - Ponte sullo Stretto di Messina (modello globale)

Ricostruzione di un **modello FEM globale** del Ponte sullo Stretto di Messina —
il futuro ponte sospeso a campata unica più lungo del mondo (3.300 m) — a
partire dai **dati pubblici del Progetto Definitivo 2011** (Eurolink/COWI). Si
calcolano l'equilibrio sotto peso proprio, la risposta sotto carichi statici di
esercizio e i modi propri di vibrazione, confrontandoli con i risultati del
modello ufficiale **IBDAS** riportati nella relazione di validazione PS0005.

Il modello usa gli [elementi cavo](it-33-cavi-ponti-strallati-sospesi.html): due
piani di cavi principali, **pendini** a sola trazione (in campata centrale *e*
nelle campate laterali sospese), impalcato a **tre cassoni**, torri a **gambe
inclinate** e soluzione non lineare (Newton-Raphson).

> **Modello di studio.** Le inerzie esecutive non sono pubbliche: si usano i
> valori rappresentativi delle **sezioni reali del modello IBDAS** (Appendice
> D-1 di PS0002). Le **masse** derivano interamente dai **carichi permanenti
> reali** (PG0022): *non* è stata introdotta alcuna massa fittizia di taratura.

![Modello globale 3D del Ponte sullo Stretto](images/messina_3d.png)

## 1. Geometria di progetto

Campata centrale sospesa di 3.300 m tra due torri alte ~383 m, con due campate
laterali sospese di 183 m. Quattro cavi principali su due piani verticali
(interasse 52 m) scendono dalle selle in sommità (382,6 m) alla mezzeria (81,1
m): freccia **f = 301,5 m** (f/L ≈ 1/10,9).

| Grandezza | Valore |
|-----------|-------:|
| Campata centrale *L* | 3.300 m |
| Campate laterali sospese | 183 m (×2) |
| Lunghezza sospesa totale | 3.666 m |
| Altezza torri (sella cavo) | 382,6 m |
| Quota impalcato (mezzeria) | 74,34 m |
| Quota cavo in mezzeria | 81,1 m |
| Freccia del cavo *f* | 301,5 m (f/L ≈ 1/10,9) |
| Larghezza impalcato | 60,4 m |
| Interasse piani dei cavi | 52 m (z = ±26 m) |
| Cavi principali | 4 (2 piani × 2 cavi) |
| Area metallica per cavo | 1,015 m² |
| Diametro cavo (con avvolgimento) | 1,263 m |

![Prospetto longitudinale del modello globale](images/messina_elevation.png)

L'impalcato è formato da **tre cassoni**: due cassoni stradali (z = ±18,72 m) e
un cassone ferroviario centrale (z = 0). I traversi si prolungano fino a z =
±26 m, dove si agganciano i pendini ai piani dei cavi.

![Sezione trasversale: tre cassoni](images/messina_section.png)

![Pianta dell'impalcato: 3 cassoni, traversi e pendini ai bordi](images/messina_deck_plan.png)

## 2. Carichi permanenti (PG0022, modello 3.3f)

I carichi permanenti per unità di lunghezza derivano dal documento PG0022
«Riassunto carichi permanenti per il Modello Globale». Determinano sia la
statica del cavo sia la massa per l'analisi modale.

| Contributo | kN/m |
|------------|-----:|
| Travate stradali — PP (×2) | 97,8 |
| Travata ferroviaria — PP | 31,6 |
| Traversi — PP (T1, ogni 30 m) | 53,8 |
| Saldature + pittura + morsetti — PP | 7,8 |
| Manto stradale — PN | 20,0 |
| Attrezzatura stradale — PN (×2) | 36,2 |
| Attrezzatura ferroviaria — PN | 18,1 |
| **Totale impalcato sospeso  g₁+g₂** | **265,3** |
| Cavi principali — PP+PN (4 cavi) | 324,3 |
| **Carico verticale totale  w** | **589,6** |

Carico verticale totale sul sistema di sospensione: **w = 589,6 kN/m**, pari a
una massa di circa 60,1 t/m.

## 3. Idealizzazione strutturale

Modello tridimensionale «a lisca di pesce» (grillage), assi: X longitudinale,
Y verticale, Z trasversale.

- **Cavi principali**: due piani (z = ±26 m), ciascuno equivalente ai 2 cavi
  reali (area 2,03 m²), discretizzati in elementi cavo a grandi spostamenti.
- **Pendini**: cavi verticali a sola trazione, in **campata centrale** (dal cavo
  principale) *e* nelle **campate laterali** (dal cavo di ritenuta — vedi §4).
- **Impalcato a tre cassoni**: due cassoni stradali (z = ±18,72 m) e un cassone
  ferroviario (z = 0), travi longitudinali collegate da traversi prolungati fino
  a z = ±26 m. La separazione dei cassoni dà la rigidezza torsionale e laterale.
- **Torri**: due gambe **inclinate verso l'interno** (da z = ±39,23 m alla base,
  quota 18 m, fino a z = ±26 m alla sella, quota 382,6 m), con traversi di
  portale; basi incastrate.
- **Campate laterali e ancoraggi**: cavo di ritenuta dalla sella ai blocchi di
  ancoraggio; impalcato laterale sospeso dai pendini e appoggiato alle strutture
  terminali.

![Vista frontale della torre: gambe inclinate e impalcato a 3 cassoni](images/messina_tower_front.png)

### Sezioni reali del modello IBDAS (PS0002, App. D-1)

Valori rappresentativi (mediane robuste, E = 210 GPa, outlier OCR filtrati).
*Iy* = flessione laterale (piano orizzontale), *Iz* = flessione verticale. Il
cassone stradale è **rigidissimo lateralmente (Iy = 9 m⁴) e flessibile
verticalmente (Iz = 0,44 m⁴)**: impalcato aerodinamico.

| Elemento | A [m²] | Iy [m⁴] | Iz [m⁴] | J [m⁴] | E [GPa] |
|----------|------:|------:|------:|------:|------:|
| Cassone stradale (×2) | 0,576 | 9,00 | 0,44 | 1,02 | 210 |
| Cassone ferroviario | 0,332 | 0,68 | 0,60 | 1,08 | 210 |
| Traversi impalcato | 0,50 | 1,38 | 1,67 | 2,85 | 210 |
| Gambe torre | 7,21 | 327,7 | 108,2 | 164 | 210 |
| Traversi torre | 2,39 | 63,9 | 27,4 | 50 | 210 |
| Cavo principale (per piano) | 2,03 | — | — | — | 200 |
| Pendini | 0,018 | — | — | — | 200 |

## 4. Vincoli e schema statico

La corretta definizione dei vincoli è l'aspetto più delicato:

- **Selle in sommità**: il nodo terminale del cavo coincide con la sella ed è
  vincolato in traslazione (sella rigida). La torre porta la reazione verticale;
  la componente orizzontale è equilibrata dal cavo di ritenuta.
- **Basi delle torri**: incastrate (si trascura la flessibilità delle
  fondazioni, descritta in PB0030/PB0031).
- **Ancoraggi**: cavi di ritenuta bloccati nei blocchi di ancoraggio.
- **Impalcato sospeso**: appeso ai pendini per tutta la lunghezza (−1.833 ÷
  +1.833 m), appoggiato in verticale e in trasversale alle torri e alle
  **strutture terminali** agli estremi; un unico ritegno longitudinale in
  mezzeria (impalcato «flottante»). Le campate laterali **non** sono a sbalzo:
  sono **sospese dal cavo di ritenuta** (i pendini coprono tutta la lunghezza
  sospesa, confermato dai grafici di PS0005) e appoggiate ai terminali — **senza
  pile** intermedie.

> **Nota sull'errore tipico.** Un tentativo precedente forniva frequenze errate
> perché l'impalcato era incastrato a entrambi gli estremi con tutte le rotazioni
> bloccate (irrigidimento artificiale) e, per riabbassare le frequenze, era stata
> aggiunta una massa fittizia di 450.000 kN/m (~50 volte il carico reale). Qui
> quei due artifici sono rimossi: vincoli fisici e massa reale.

## 5. Analisi statica

### 5.1 Peso proprio (configurazione a fine costruzione)

Soluzione non lineare (Newton-Raphson): converge in 5 iterazioni. Il cavo
mantiene la forma parabolica di progetto e l'impalcato si abbassa di soli 0,44 m
in mezzeria. La validazione si appoggia alla teoria classica del cavo parabolico:
**H = w·L²/(8·f)**.

| Grandezza | FEM | Teoria / nota |
|-----------|----:|:--------------|
| Freccia del cavo (deformata) | 301,9 m | 301,5 m (geometrica) |
| H — componente orizzontale (per piano) | 1.334 MN | 1.331 MN = w L²/8f |
| Tiro massimo cavo, per piano (alla sella) | 1.416 MN | — |
| Tiro massimo per singolo cavo reale | 708 MN | — |
| Tensione massima nel cavo | 698 MPa | ammiss. ~700–840 MPa |
| Tiro nei pendini | 4,0 – 7,4 MN | — |
| Abbassamento impalcato in mezzeria | −0,44 m | — |

![Deformata sotto peso proprio](images/messina_deformed.png)

![Deformata 3D sotto peso proprio (indeformato in grigio)](images/messina_3d_deformed.png)

**Confronto con IBDAS (PS0005, fine costruzione).** Le forze del modello sono
dello stesso ordine ma ~12–14 % più alte:

| Grandezza (per piano di cavo) | beamfeapy | IBDAS | scarto |
|-------------------------------|----------:|------:|------:|
| Forza nel cavo in mezzeria | 1.334 MN | 1.170 MN | +14 % |
| Forza nel cavo alla sella | 1.416 MN | ≈1.260 MN | +12 % |
| Carico permanente totale *w* | 590 kN/m | ≈515 kN/m | +15 % |

Lo scarto è spiegato dal carico permanente: il valore dedotto da PG0022 è
leggermente conservativo. A parità di carico la meccanica resta esatta (H =
wL²/8f entro lo 0,2 %).

Il grafico ufficiale della forza nel cavo (modello ADINA/IBDAS, PS0005) mostra
**N ≈ 1.170 MN in mezzeria** che cresce fino a **~1.260 MN alle torri**, con il
caratteristico picco alla sella: lo stesso andamento del modello beamfeapy (1.334
÷ 1.416 MN), traslato verso l'alto per via del carico permanente più alto.

![Forza nel cavo principale — modello ufficiale ADINA/IBDAS (da PS0005)](images/messina_ibdas_cableforce.png)

### 5.2 Carichi di esercizio (traffico e vento)

Oltre al peso proprio si analizzano due casi di carico statico di riferimento:
- **QV** — traffico verticale: 24 kN/m per cassone stradale + 48 kN/m sul
  ferroviario (96 kN/m totali) su tutto l'impalcato;
- **QT** — azione trasversale tipo vento: ~15 kN/m totali sull'impalcato.

| Caso | Risultato |
|------|----------:|
| G — abbassamento mezzeria | −0,44 m |
| G+QV — abbassamento mezzeria | −4,10 m (incremento da traffico −3,66 m ≈ L/900) |
| G+QV — momento flettente max nei cassoni stradali | 253 MNm |
| G+QV — tiro massimo nel cavo | 1.628 MN/piano (+211 MN) |
| G+QT — spostamento trasversale max impalcato | 8,3 m |

L'impalcato è molto rigido verticalmente grazie ai cavi (incremento di sola
freccia L/900 sotto traffico), mentre la risposta **trasversale** è la più
sensibile: lateralmente l'impalcato non è sostenuto dai cavi e si comporta come
una trave su 3.300 m di luce, con spostamenti dell'ordine di alcuni metri.

![Analisi statica: deformata verticale, momento flettente, deformata laterale](images/messina_static.png)

## 6. Analisi modale

L'analisi linearizza i cavi attorno all'equilibrio sotto peso proprio (la
rigidezza geometrica del cavo, proporzionale al tiro, fornisce la rigidezza
trasversale). La massa deriva dai carichi permanenti reali. I primi quattro modi
globali sono confrontati con i parametri modali IBDAS/ADINA di PS0005 (par. 7.2).

| # | Tipo di modo | IBDAS | ADINA | beamfeapy | T [s] | scarto |
|---|--------------|------:|------:|----------:|------:|------:|
| 1 | trasversale (laterale) | 0,0309 | 0,0313 | 0,0322 | 31,0 | +4 % |
| 2 | verticale antisimmetrico | 0,0569 | 0,0585 | 0,0598 | 16,7 | +5 % |
| 3 | longitudinale | 0,0593 | 0,0593 | 0,0662 | 15,1 | +12 % |
| 4 | verticale simmetrico | 0,0809 | 0,0809 | 0,0877 | 11,4 | +8 % |

*Frequenze in Hz; scarto riferito a IBDAS.*

![Confronto delle frequenze dei primi 4 modi globali](images/messina_modal_bar.png)

![Forme modali fondamentali](images/messina_modes.png)

![Forme modali fondamentali in 3D (indeformato in grigio)](images/messina_modes_3d.png)

Le stesse quattro forme modali nel **modello ufficiale ADINA/IBDAS** (schermate
tratte dal documento di validazione PS0005) confermano **tipo, gerarchia e forma**
di ciascun modo: la semionda laterale in pianta (modo 1), l'antisimmetrica
verticale con nodo in mezzeria (modo 2), il modo longitudinale (modo 3) e la gobba
simmetrica verticale (modo 4).

![Forme modali del modello ufficiale ADINA/IBDAS (da PS0005)](images/messina_ibdas_modes.png)

**Conferma teorica.** Il primo modo verticale antisimmetrico di un cavo
parabolico ha frequenza f ≈ √(g/8f) = 0,0638 Hz, **indipendente dalla massa**:
dipende solo dalla freccia. Il modello (0,0598 Hz) riproduce questa relazione ed
è coerente con l'IBDAS (0,0569 Hz).

## 7. Discussione e limiti

- Il modo fondamentale (trasversale) è in eccellente accordo (+4 %); i modi
  verticali entro +5÷8 %.
- Lo scarto residuo verso l'alto deriva soprattutto dall'aver vincolato
  rigidamente le selle e incastrato le basi delle torri: trascurando la
  flessibilità di torri e fondazioni il modello è più rigido del reale. La
  frequenza verticale di un ponte sospeso dipende dalla freccia e **non** dalla
  massa, quindi lo scarto non è imputabile alla massa.
- Coerentemente, le forze statiche nel cavo sono ~13 % superiori a IBDAS: il
  carico permanente da PG0022 è leggermente conservativo. Massa (un po' alta) e
  rigidezza (un po' alta per i vincoli rigidi) agiscono in verso opposto sulle
  frequenze, lasciando uno scarto netto contenuto.
- Restano semplificati il profilo altimetrico dell'impalcato, i dispositivi di
  vincolo (buffer alle torri) e gli effetti aerodinamici/sismici.

**Punto chiave:** l'accordo entro ~4÷12 % sui modi e l'esattezza della meccanica
del cavo (H = wL²/8f entro 0,2 %) sono ottenuti con le **sezioni reali IBDAS** e
**senza alcuna calibrazione della massa**, usando solo dati pubblici.

## 8. Riferimenti

- **PG0022** — Riassunto carichi permanenti per il Modello Globale (Progetto
  Definitivo, rev. F0, 2011).
- **PS0002** — Modello globale IBDAS, descrizione (Appendice D-1, sezioni delle
  travi).
- **PS0005** — Validazione del modello globale agli elementi finiti (parametri
  modali e risultati statici IBDAS/ADINA).
- **PB0030 / PB0031** — Matrici equivalenti di rigidezza e smorzamento
  suolo-fondazioni.
- H.M. Irvine, *Cable Structures*, MIT Press, 1981.
- A.G. Pugsley, *The Theory of Suspension Bridges*, 2ª ed., Edward Arnold, 1968.

## 9. Come rigenerare

```bash
python scripts/messina_global_model.py   # costruzione modello + analisi statica e modale
python scripts/messina_figures.py        # tutte le figure di questa pagina
```

Vedi anche il [ponte sospeso Vincent Thomas](it-35-ponte-sospeso-vincent-thomas.html)
e la [teoria degli elementi cavo](it-33-cavi-ponti-strallati-sospesi.html).
