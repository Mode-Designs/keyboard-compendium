# EVALUATIONS.md — evaluation methods

How to verify a change to this site. It is a static MkDocs (Material) reference site with no
application logic or generated output, so evaluation covers three things: the build is correct,
each page renders as intended, and factual data is accurate. The strict build always applies; add
the other methods that match the surface you touched (see the table at the end).

Two standing rules. Anything that changes what a page renders should be confirmed visually (a
static site is fully judgeable from a screenshot, no recordings needed). And factual values are
never invented: compatibility, availability, version, production-year, status, and sourcing values
come verbatim from the maintainer's source spreadsheet (`references/Keyboard Compendium.xlsx`,
gitignored and local-only) or the maintainer directly. An unresolved fact is unverified pending
confirmation, never a best guess (including "obvious" forward/backward compatibility or overlap
between boards).

## Methods

### 1. Strict build — required, every change

- **When:** always.
- **How:** `mkdocs build --strict` from the repo root. Validates internal links, missing files
  (images, CAD downloads), bad references, nav entries, and markdown-extension config; mirrors CI
  (`.github/workflows/build.yml`).
- **Pass:** exit 0.
- **Does not cover:** external URLs, font/favicon runtime paths (method 3), or factual accuracy
  (method 5). `site/` is a gitignored build artifact; never commit it.

### 2. Page render check

- **When:** any change to what a page renders — board/part pages, Components tables, status chips,
  Availability cards, admonitions, prose, or nav structure.
- **How:** `mkdocs serve` (http://127.0.0.1:8000), or build and open `site/`; view each affected
  page.
- **Pass:** tables, chips, cards, and admonitions present and correctly formatted; images load; no
  raw markdown; structure follows CONTRIBUTING.md (component-category order, display-form names,
  lowercase slugs, `.webp` images).

### 3. Chrome runtime check — fonts / favicon

- **When:** changes to `docs/theme/**`, the `@font-face` `url()`s in `docs/stylesheets/extra.css`,
  or `mkdocs.yml` `theme.logo` / `theme.favicon` / `palette` / `font`.
- **How:** build, serve the built site, load a page, and confirm the custom font is applied and the
  favicon shows in the tab, with no 404s on `theme/*` or font assets.
- **Pass:** font applied, favicon present, zero theme-asset 404s.
- **Why separate:** `--strict` does not validate font/theme paths; a wrong path is a silent runtime
  404 that only this check catches.

### 4. Hover-preview check

- **When:** changes to part-link previews — `{ data-preview }` attributes on Components-table
  links, the `material.extensions.preview` block in `mkdocs.yml`, or the `parts/**` targets it
  points at.
- **How:** `mkdocs serve`, open a board's Components page, and hover a `{ data-preview }` link.
- **Pass:** the preview card shows that part page's content (e.g. its Availability section).
  Preview targets are limited to `parts/**`.

### 5. Factual-data provenance

- **When:** any change to a factual value — compatibility, availability tokens (`Mode`, `Support`,
  `Have it Made`, `Third Party`, `Unavailable`), version (`Original`, `Updated`, `Standard`),
  production years, status (`Legacy`, `Current`), or sourcing.
- **How:** not derivable from the repo. The value must come verbatim from the source spreadsheet in
  `references/`; where present, the published value must exact-match the source. A value not in the
  source is flagged for the maintainer, not invented.
- **Pass:** every changed fact exact-matches the source or is flagged unresolved for maintainer
  confirmation. Nothing inferred, derived from overlap, or fabricated.

## Which methods by surface

| Surface touched | Methods |
| :--- | :--- |
| Page content: `docs/keyboards/**`, `docs/parts/**`, `docs/*.md` (prose, tables, chips, cards) | 1, 2 |
| Factual data values (compatibility / availability / version / production years / status / sourcing) | 1, 2, 5 |
| Published files: `docs/files/**` (CAD), `docs/assets/**` (`.webp` images) | 1, 2 (+ 5 if the file embodies a data fact) |
| Part-link hover previews (`{ data-preview }`, `material.extensions.preview`, `parts/**` targets) | 1, 4 |
| Site chrome (`docs/theme/**`, `extra.css` `@font-face`, `mkdocs.yml` theme / palette / font) | 1, 3 |
| `mkdocs.yml` nav / plugins / markdown config | 1, 2 |
| Repo meta only (`README.md`, `CONTRIBUTING.md`, `LICENSES/`, `.github/`) with no `docs/` render impact | 1 |
| Anything else | 1, plus a look at the affected page |
