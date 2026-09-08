# feagent-docs

Documentazione pubblica di **feagent** — libreria FEM per travi 3D (Euler-Bernoulli / Timoshenko): sezioni variabili, rilasci, termica, precompressione da cavo, load case, I/O Excel, plot Plotly, analisi modale.

Questa repository contiene **solo la documentazione** (sorgenti Markdown, immagini e allegati): il codice sorgente della libreria resta nella repository privata `feagent`.

- Sito pubblicato: **https://domenicogaudioso.github.io/feagent-docs/**
- Lingue: Italiano (it-*) e Inglese (en-*)
- Licenza: [PolyForm Noncommercial 1.0.0](LICENSE) (come la repository privata)

## Struttura

| Cartella | Contenuto |
|---|---|
| `en-*.md`, `it-*.md` | Pagine di documentazione (Markdown, build automatico GitHub Pages / just-the-docs) |
| `images/` | Immagini delle pagine (modelli, diagrammi, screenshot) |
| `assets/` | Allegati (report dimostrativi .docx dei casi di studio) |
| `_includes/` | MathJax per le formule |
| `img/` | Logo |

## Aggiornamento

I sorgenti rispecchiano la cartella `docs/` della repository privata `feagent`
(branch di lavoro `feagent`). La sincronizzazione e' automatica: dalla root
della repository privata

```bash
python scripts/sync_public_docs.py --commit -m "docs: ..."
```

copia le pagine riscrivendo i link `*.md` in `*.html`, aggiorna immagini e
allegati, rimuove i file non piu' presenti nella sorgente e si blocca se una
pagina contiene dati di commesse reali o nomi di progettisti.
