# CLAUDE.md — Portfolio Madalin Luciu

Sito portfolio statico (nessun build step, nessuna dipendenza). Stile: scuro ad alto contrasto, gradiente nero → grigio, card "FIG." ruotate, micro-interazioni al passaggio del mouse.

## Struttura

```
index.html          → solo struttura + logica di rendering (JS inline)
style.css           → solo presentazione
data/content.json   → TUTTI i contenuti (testi, date, esperienze, competenze, link)
```

`index.html` carica i contenuti con `fetch('./data/content.json')` e genera il DOM.

## Regole obbligatorie

1. **Le modifiche ai contenuti si fanno esclusivamente in `data/content.json`.**
   Testi, date, ruoli, esperienze, progetti, competenze, link, email, etichette di navigazione:
   tutto va modificato nel JSON. **Non alterare `index.html` né `style.css`** per un cambio di contenuto.
2. Non inserire testo visibile hardcoded in `index.html` o `style.css` (unica eccezione: il messaggio
   di errore mostrato quando il JSON non si carica).
3. Il JSON deve restare valido (niente commenti, niente virgole finali).
4. **Prima di chiudere un task, verifica che la pagina si carichi senza errori** (vedi sotto).
5. Modifiche a `index.html` / `style.css` solo se l'utente chiede esplicitamente un cambio di
   layout, stile o funzionalità.

## Schema di `data/content.json`

| Chiave | Contenuto |
|---|---|
| `meta` | `lang`, `title` (tab del browser), `description` (SEO) |
| `ui` | etichette di interfaccia (`figPrefix`, `scrollHint`, …) |
| `nav[]` | `label`, `href`, `covers[]` (id delle sezioni che attivano la voce) |
| `socials[]` | `label`, `url` |
| `hero` | `name`, `bubble` (fumetto giallo), `bubbleSad` (testo mostrato per ~1 s dopo il click sulla ×, prima che il fumetto sparisca), `dots[]` ("unisci i puntini": `letter` = indice del carattere in `name`, da 0, spazi inclusi; `from` / `to` = numeri ai due capi del tratto mancante), `tags[]` con `text` e `tone` (`accent` \| `dark` \| `muted`) |
| `sections[]` | `id`, `indexLabel`, `title`, `titleFaded`, `layout` (`scatter` \| `cluster`), `cta` opzionale (`label`, `href`, `tone`: `violet` \| `cyan`), `figures[]` |
| `contact` | `id`, `indexLabel`, `title`, `titleFaded`, `text`, `email`, `cta` |
| `footer` | `text` |

I numeri "FIG. n" e l'indice in basso a sinistra sono calcolati automaticamente.

### Tipi di figura (`figures[].type`)

| type | campi |
|---|---|
| `window` | `url`, `eyebrow`, `title`, `subtitle`, `body`, `meta[]` (`label`, `value`) |
| `stat` | `value`, `label`, `note`, `items[]` opzionale (etichette: stringa, oppure `{ "text", "href" }` per renderla un link esterno con freccia ↗), `disclaimer` opzionale (nota in piccolo sotto le etichette) |
| `checklist` | `title`, `items[]` (`text`, `checked`) |
| `ticket` | `key`, `title`, `status`, `tags[]`, `footer` |
| `terminal` | `title`, `lines[]` (`text`, `tone`: `cmd` \| `dim` \| `ok` \| `warn` \| `err`) |
| `project` | `eyebrow`, `title`, `body`, `tags[]`, `link` opzionale (`label`, `href`) |
| `report` | `title`, `rows[]` (`label`, `status`: `ok` \| `warn` \| `err`) |
| `skill` | `name`, `category`, `level` (0–100) |

Il layout `scatter` è pensato per gruppi di 4 figure; `cluster` per griglie di skill.
Un `type` sconosciuto viene saltato con un `console.warn`.

## Verifica obbligatoria prima di chiudere un task

`fetch()` non funziona da `file://`: serve un server locale.

1. Validare il JSON, es. in PowerShell:
   `Get-Content data/content.json -Raw | ConvertFrom-Json | Out-Null`
2. Avviare un server nella cartella del progetto, ad es. `python -m http.server 8000`
   oppure `npx serve .` e aprire http://localhost:8000
3. Controllare che:
   - non compaia il banner rosso di errore "data/content.json non caricato";
   - la console del browser non mostri errori;
   - nome, sezioni e contatti vengano renderizzati.
   In headless: `msedge --headless=new --dump-dom http://localhost:8000` e verificare che
   il DOM contenga i contenuti e che `#loadError` abbia ancora l'attributo `hidden`.
