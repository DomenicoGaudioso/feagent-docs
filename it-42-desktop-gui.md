---
layout: default
title: "42 - Interfaccia desktop (feagent gui)"
parent: Italiano
nav_order: 42
---

# 42 - Interfaccia desktop

`feagent gui` avvia l'**interfaccia grafica** del solutore: un server locale
(solo sul tuo PC, indirizzo `127.0.0.1`) e una finestra applicazione nel
browser. Si lavora come in un programma FEM classico: albero del modello a
sinistra, vista 3D al centro, proprietà a destra, menu e barra strumenti in
alto, messaggi in basso.

![Interfaccia desktop: albero, vista, proprietà](images/gui_moment.png)

Il modello si può costruire in tre modi, liberamente mescolati:

* **disegnandolo** nella vista (nodi e travi con aggancio alla griglia e ai nodi);
* **in forma tabellare**, come in Excel, con copia e incolla da e verso Excel;
* **importandolo** da un workbook feagent, da tabelle SAP2000/MIDAS/Robot o da
  una specifica JSON del connettore AI.

## Avvio

```bash
pip install "feagent[gui]"      # aggiunge pandas e openpyxl
feagent gui                     # modello vuoto
feagent gui telaio.xlsx         # apre un modello
```

Su Windows si può usare anche `feagent-gui` (senza finestra di terminale),
adatto a un collegamento sul desktop. L'interfaccia si apre in una finestra di
Edge o Chrome in modalità applicazione; se nessuno dei due è presente, nel
browser predefinito.

| Opzione | Effetto |
|---|---|
| `--port 8777` | porta preferita (se occupata se ne sceglie una libera) |
| `--no-browser` | non apre la finestra: stampa l'indirizzo da aprire |
| `--keep-alive` | il server resta attivo anche a finestra chiusa |

Il server si ferma con **File > Esci**, chiudendo la finestra (dopo circa 45
secondi senza segnali dall'interfaccia) o con Ctrl+C nel terminale. Ascolta
solo in locale e ogni chiamata richiede il token di sessione generato
all'avvio, quindi una pagina web qualsiasi aperta nel browser non può leggere
o modificare i tuoi file.

## Il documento del modello

Quello che si vede nell'albero e nelle tabelle è il **formato Excel di
feagent** (fogli Node, Material, Section, Element, Support, carichi,
Combination; vedi [Excel I/O](it-11-excel-io.html)). Salvare in `.xlsx` produce
quindi un workbook che si rilegge senza differenze da Python
(`Model.from_excel`), dalla riga di comando (`feagent solve`) e dal connettore
AI. In alternativa si salva un progetto `.feagent.json` con lo stesso
contenuto. L'unico foglio in più è **LoadCase**, che elenca anche i casi di
carico ancora senza carichi; la libreria lo ignora.

## Menu e barra strumenti

![Menu File con i sottomenu di importazione](images/gui_menu.png)

| Menu | Contenuto |
|---|---|
| File | nuovo, apri, file recenti, salva, salva con nome, importa, esporta, esci |
| Modifica | annulla e ripeti (60 passi), selezione, elimina, copia e trasla |
| Vista | 3D, pianta, prospetto, laterale, adatta; numeri, assi locali, vincoli, carichi, griglia |
| Modello | strumenti di disegno, nodi per coordinate, griglia strutturale, dividi, unisci nodi, proprietà, materiali e sezioni |
| Carichi | casi di carico, carichi nodali, distribuiti, concentrati, termici, peso proprio, combinazioni |
| Analisi | controllo del modello (codici E01-E63 di `feagent check`), statica per casi, combinazione libera, combinazioni, modale, buckling |
| Risultati | deformata, N, Vy, Vz, T, My, Mz, reazioni, modi, tabelle |

**Esporta** scrive il modello per OpenSees (Tcl e Python), SAP2000, MIDAS,
Robot e Straus7, i risultati in Excel, il tabulato di calcolo e la relazione
Word. Se i dialoghi file nativi non sono disponibili, apertura e salvataggio
passano per il caricamento e lo scaricamento del browser.

## Albero del modello

Raggruppa nodi, elementi, materiali, sezioni, vincoli, cedimenti, casi di
carico (con i carichi per tipo) e combinazioni, poi le analisi e i risultati.

* **clic**: seleziona nella vista (un materiale o una sezione selezionano le
  travi che li usano; un caso di carico diventa il caso mostrato);
* **doppio clic**: apre la tabella corrispondente, lancia un'analisi o mostra
  un risultato;
* **tasto destro**: azioni del nodo (nuovo caso, nuova combinazione, rinomina,
  assegna alla selezione, sezione parametrica, nuovo materiale).

## Vista 3D

| Azione | Comando |
|---|---|
| ruota | tasto destro trascinato |
| sposta | tasto centrale, oppure Maiusc + tasto destro |
| zoom | rotella (centrata sul cursore) |
| seleziona | clic; Ctrl+clic aggiunge o toglie |
| selezione a finestra | trascina verso destra (elementi interamente dentro) |
| selezione per intersezione | trascina verso sinistra (elementi toccati) |
| viste | tasti 1 (3D), 2 (pianta), 3 (prospetto), 4 (laterale), F (adatta) |

L'asse verticale (Y o Z) si deduce dalla direzione dei carichi e si può
fissare in **Vista > Opzioni**. I carichi disegnati sono quelli del **caso
attivo**, scelto nella barra strumenti o nell'albero.

### Disegnare

* **N** (Disegna nodi): un clic inserisce un nodo sul piano di lavoro, sul
  passo della griglia.
* **B** (Disegna travi): clic sul primo punto e sui successivi, come una
  polilinea; il tasto destro o Esc chiudono la catena. I punti si agganciano
  ai nodi esistenti; le nuove travi usano il materiale e la sezione scelti
  nella barra strumenti.
* Il piano di lavoro segue la vista (pianta, prospetto) oppure si fissa in
  **Vista > Opzioni**, con quota e passo della griglia.

Per le geometrie regolari **Modello > Genera griglia strutturale** crea travi
continue, telai piani e telai 3D da elenchi di luci (`3*6 4.5` significa tre
luci da 6 e una da 4,5), con incastri o cerniere alla base.

![Deformata di un telaio 3D con scala dei colori](images/gui_deformed.png)

## Tabelle

![Tabella degli elementi](images/gui_table.png)

Ogni foglio si apre in una scheda (doppio clic nell'albero, oppure
**Modello > Tabelle** e **Carichi > Tabelle dei carichi**). La griglia regge
modelli grandi perché disegna solo le righe visibili.

| Tasto | Effetto |
|---|---|
| frecce, Tab, PagSu/PagGiù | spostamento |
| digitare, Invio, F2 | modifica della cella |
| Maiusc + frecce, trascinamento | selezione di un blocco |
| Ctrl+C / Ctrl+V | copia e incolla, anche da e verso Excel |
| Canc | svuota le celle selezionate |
| Ctrl+Canc | elimina le righe selezionate |

Se il blocco incollato ha una riga di intestazione con i nomi delle colonne,
i valori vanno nelle colonne giuste anche se l'ordine è diverso; le righe in
più vengono aggiunte. Selezionare le righe di Nodi o Elementi seleziona gli
oggetti nella vista, e viceversa.

## Proprietà

Il pannello di destra cambia con la selezione: per un nodo coordinate,
vincolo, carichi, spostamenti e reazioni; per una trave nodi, materiale,
sezione, formulazione di Timoshenko, svincoli, assi locali, carichi ed
estremi delle azioni interne; per una selezione multipla le azioni
disponibili. Senza selezione mostra il riepilogo del modello.

## Risultati

Dopo l'analisi la barra strumenti propone il risultato (caso, combinazione o
modo), la grandezza e la scala.

* **Deformata**: linea elastica calcolata con le funzioni di forma degli
  elementi (svincoli compresi), colorata con lo spostamento.
* **Azioni interne**: diagrammi di N, Vy, Vz, T, My e Mz nel piano locale
  dell'elemento, con massimo e minimo. I **momenti si disegnano dalla parte
  delle fibre tese**: per travi orizzontali con y locale verso l'alto i momenti
  negativi stanno sopra la trave, i positivi sotto.
* **Reazioni**: frecce con i valori e somma delle reazioni.
* **Modi**: forme modali animate, frequenze e masse partecipanti, oppure
  moltiplicatori critici del buckling.
* **Tabelle**: spostamenti, reazioni (con la somma) e azioni interne lungo gli
  elementi, copiabili in Excel.

Se il modello cambia dopo l'analisi, l'albero e la vista segnalano che i
risultati vanno aggiornati (F5).

## Scorciatoie

| Tasti | Comando |
|---|---|
| Ctrl+N, Ctrl+O, Ctrl+S, Ctrl+Maiusc+S | nuovo, apri, salva, salva con nome |
| Ctrl+Z, Ctrl+Y | annulla, ripeti |
| F5 | statica per casi |
| F6 | combinazioni |
| S, N, B | seleziona, disegna nodi, disegna travi |
| Canc | elimina la selezione |
| Esc | chiude la catena di travi, torna alla selezione, deseleziona |

## Rapporto con l'interfaccia Streamlit

L'app Streamlit ([pagina 28](it-28-web-ui.html)) resta disponibile, ma
l'interfaccia desktop la sostituisce per il lavoro quotidiano: disegno nella
vista, tabelle con copia e incolla, annulla e ripeti, dialoghi file nativi,
nessuna dipendenza oltre a pandas e openpyxl.
