# Ibruktagningsprotokoll: notes for AI assistants (Claude, Copilot, Codex, etc.)

A mobile-first, client-only web form for Finnish commissioning inspection records of electrical installations.
Students at Axxell fill it in on their phones.

## ⚠️ Rule #1: never put personal data (PII) in git or on GitHub

A filled-in protocol contains real people's names, phone numbers, e-mail addresses and street addresses. That
data lives only in the user's browser and in share links (the `#…` part of the URL).

- Never commit, paste or upload real share links or their `#` payloads, printouts or PDFs of filled-in
  protocols. `*.pdf` is gitignored.
- Use made-up data in tests, examples and commit messages, for example the data from Tukes' public example
  ("Oy Sähkö Ab", "Ville Virtanen").
- Never add analytics, logging or anything else that sends form data to a server.

## How it works

- `index.html` is the whole app (HTML, CSS and JS, no build step). The QR library (qrcode-generator 1.4.4) is
  loaded from cdnjs.
- `SECTIONS` is the schema. Field helpers: `T` (text; `type` can be `num`, `tel`, `email`, `date` or `area`),
  `C` (checkbox), `R` (choice), `G` (side-by-side grid), `H` (sub-heading), `N` (note), `TBL` (repeating table
  rows with `min`, `max`, `fixed` and `cols`). Options: `req` (red * marker), `showIf:[key,value]`,
  `screenOnly`, `span` (table column width out of 4), and `printLabel` / `printSign` for print.
- **Link format (keyed, backwards compatible):** `#` plus `J` (deflate-raw) or `j` (uncompressed) plus base64url of
  a JSON object. Text fields are stored as `"key":"value"`, checkboxes as `1`, choices as the 1-based option
  number, and tables as `"key":[[colKeys…],row,…]`. A row is an array of cells (`""`, text or `1`), or `0` for an
  empty row. `toObj`/`fromObj` convert between this and `S`.
  - Fields can be added, removed and reordered freely. **Never rename a key or reuse it for a new meaning.**
    Add removed keys to `RETIRED` with a readable label.
  - On load, `fromObj` puts values it can't place (unknown key or column, wrong type, choice option out of range)
    into `unknown`. `showNotice` lists them above the form. They are never put into `S`, so saves and prints drop
    them. The original link is kept in the address bar until the first edit or until the notice is closed.
  - Choice options are stored by position: only append options, never reorder them.
- State: `S` (key → value). It's saved to `localStorage['ibp.v1']` and the URL hash. A replaced draft goes to
  `ibp.backup`, and open/closed sections are in `ibp.open`.
- Print: `renderPrint()` builds a separate `#print` DOM (a Tukes-style A4 layout) after every save. Print CSS shows
  only `#print`. Add `class="pp"` to `body` to preview it on screen. Check that the Tukes example data still
  fits on one A4 page after changing the layout.
- Help texts: Markdown in `<script type="text/markdown">` blocks (`#md-general`, `#md-beha`, `#md-beha200`, `#md-fluke`) in
  `index.html`, rendered by a small built-in Markdown renderer (`md()`: headings, paragraphs, bold/italic, links,
  nested lists, tables, blockquotes). `splitDoc()` splits each block at `## ` headings: `## Innan du börjar`
  → the "Innan du börjar mäta" modal, `## N. …` → the Hjälp button of form section N, `## Källor` → sources.
  Instrument blocks: text before the first `## ` plus `## Nollställ…` and `## Felkoder` go to the intro modal.
  Add an instrument by adding another `md-…` block with `data-name` and listing its id in `loadHelp()`. The first id there is shown first and is the default (ProInstall-100). Help
  texts are for ~16-year-old students: short sentences, explain *why* before *how*. Limit values must be checked
  against SFS 6000.
- Swedish UI text. Keep the wording close to ST 51.21.08 and the Tukes example.

## Local preview

`python3 -m http.server 8780 --bind 127.0.0.1` (port 8765 is used by another project on this machine).
