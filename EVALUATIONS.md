# EVALUATIONS.md — evaluation strategies

> Rulebook read by the agentic dev pipeline (goal-writer, goal-runner, notion-orchestrate,
> pr-babysit skills + the goal-evaluator agent). One section per strategy.
>
> This repo is a static MkDocs (Material) reference site. There is no application runtime,
> no database, no test framework, and no external write surface. "Correctness" here is:
> the site builds clean and strict, the affected pages render as specified, site chrome
> loads at runtime, and any factual data matches its authoritative source. The repo is
> public, so this file (and everything under `evals/`) is committed and world-readable.

## Global rules

1. **Pair a deterministic gate with a reviewable demo.** Every goal runs the check gate
   (row 1, can block merge) and, for anything that changes what a page renders, attaches a
   screenshot demo. **Screenshots only for this repo. No gifs, no recordings** (a static
   reference site is fully judgeable from stills).
2. **Never fabricate factual data; the source is the only authority.** Compatibility,
   availability, version, production-year, status, and sourcing values are authoritative
   data that come verbatim from the maintainer's source spreadsheet
   (`references/Keyboard Compendium.xlsx`, gitignored and local-only) or directly from the
   maintainer. The pipeline must never guess, infer, or derive such a fact (including
   "obvious" forward/backward compatibility or overlap between boards) to make a goal pass.
   An unresolved fact is a FAIL pending maintainer confirmation, not a best guess. See row 5.
3. **Structure and rendering are deterministic; facts are sourced.** Build, link, and render
   correctness get exact/observable assertions (rows 1-4). Factual data is verified by exact
   match against the source spreadsheet where present, or flagged for maintainer confirmation
   (row 5), never auto-asserted. There is no agent/LLM output surface in this repo.

## Fresh-worktree bootstrap

A pipeline worktree is a separate git checkout. To make it runnable:

```sh
pip install -r requirements.txt   # installs mkdocs-material (pulls in mkdocs); the only dependency
```

That is the entire bootstrap for the common case. No services, no database, no `.env`, no
generated clients, no build step beyond `mkdocs build`. CI uses Python 3.12; any Python 3.x
with the pinned `mkdocs-material` works locally.

**`references/` (the gitignored working source) is absent in a fresh worktree.** It holds the
source spreadsheet plus raw CAD (`references/design_files/`) and source images
(`references/drawings/<year-name>/`). Most goals are docs-only edits and do **not** need it.
A goal needs `references/` copied in only when it:

- **publishes a file or new page from source** (renaming CAD into `docs/files/`, images into
  `docs/assets/` as `.webp`, or creating a part/board page from `templates/part.md`); or
- **adds or changes a factual data value** (it must be checked against the source spreadsheet,
  see row 5).

For those goals, copy `references/` from the maintainer's main working checkout (the regular
non-worktree clone where it lives) into the worktree root before working:

```sh
cp -R "<path-to-main-checkout>/references" ./references   # stays gitignored; treat as read-only source
```

Treat `references/` as read-only source: copy it in, read from it, never modify it, and never
commit it (it and `*.xlsx` are gitignored precisely because the repo is public).

## Artifact conventions (PR embedding)

- **Markdown reports:** commit as `evals/<task-slug>.md`, link from the PR body.
- **Images (PNG):** commit under `evals/artifacts/<task-slug>/`; embed in the PR body via
  `/raw/<commit-sha>/…` image markdown (pin to the SHA).
- **Recordings:** none for this repo (see global rule 1).

> **Display vs. audit:** the committed PNGs under `evals/artifacts/` are the **audit source**
> the `goal-evaluator` checks on the filesystem. For non-trivial PRs the pipeline may also
> re-surface them inline (base64 `data:` URIs) inside a self-contained visual walkthrough
> artifact linked from the PR; trivial PRs embed via `/raw/<sha>/…`. Either way, the committed
> files, not the artifact, are what gets verified.
>
> **Public-repo caution:** `evals/` is committed and world-readable. Eval reports may reference
> the *published* value a goal changed, but must never paste raw spreadsheet contents or any
> other unpublished `references/` data into a committed file.

## Choosing rows (defaults by surface)

| Surface touched | Rows |
|---|---|
| Page content: `docs/keyboards/**`, `docs/parts/**`, `docs/*.md` (prose, tables, chips, admonitions, cards) | 1, 2 |
| Factual data values: compatibility / availability / version / production years / status / sourcing | 1, 2, 5 |
| Published files: `docs/files/**` (CAD), `docs/assets/**` (`.webp` images) | 1, 2 (+ 5 if the file embodies a data fact; copy `references/`) |
| Part-link hover previews: `{ data-preview }` links, `material.extensions.preview` config, `parts/**` targets | 1, 4 |
| Site chrome: `docs/theme/**`, `docs/stylesheets/extra.css` `@font-face`, `mkdocs.yml` `theme`/`logo`/`favicon`/`palette` | 1, 3 |
| `mkdocs.yml` nav / plugins / markdown config | 1, 2 |
| Repo meta only (`README.md`, `CONTRIBUTING.md`, `CLAUDE.md`, `LICENSES/`, `.github/`) with no `docs/` render impact | 1 |
| Nothing above fits | 1, plus a screenshot of the affected page |

---

## 1. Strict build (the check gate)     ← REQUIRED — every goal, no exceptions
- **Trigger:** every goal.
- **Harness:** `mkdocs build --strict` from the repo root (after the bootstrap `pip install`).
  Validates internal links, missing files (images, CAD downloads), bad references, nav
  entries, and markdown-extension config. This mirrors CI (`.github/workflows/build.yml`)
  exactly, so a green gate and a green PR mean the same thing.
- **Artifact:** pass/fail line (plus the failure output on failure) in `evals/<task-slug>.md`.
- **Pass bar:** exit 0.
- **Notes:** the floor; always listed alongside, never instead of, other rows. It does **not**
  validate external URLs, font/favicon runtime paths (see row 3), or factual accuracy (row 5).
  The build output dir `site/` is gitignored; never commit it.

## 2. Page render demo
- **Trigger:** any change that alters what a page renders: new or edited board/part pages,
  Components tables, status chips, Availability cards, admonitions, prose, or nav-visible
  structure.
- **Harness:** `mkdocs serve` (live preview at http://127.0.0.1:8000), navigate to each
  affected page and screenshot it; or `mkdocs build` and screenshot the page from the built
  `site/`. Capture every page the goal changed.
- **Artifact:** one PNG per affected page under `evals/artifacts/<task-slug>/`, embedded in
  the PR body via `/raw/<sha>/…`.
- **Pass bar:** each affected page renders as specified: tables, chips, cards, and admonitions
  present and correctly formatted; images load; no raw or un-rendered markdown; structure
  follows CONTRIBUTING.md (canonical component-category order, display-form names, etc.).
- **Notes:** `mkdocs serve` binds 127.0.0.1:8000; the pipeline is one sequential session, so
  the port never collides. Naming/convention conformance (lowercase slugs, name-then-year
  files vs. year-then-name display, `.webp` images) is reviewer-judged here, since the gate
  does not enforce it.

## 3. Chrome runtime check (fonts / favicon)
- **Trigger:** changes to `docs/theme/**` (logo, favicon, fonts), the `@font-face` `url()`s in
  `docs/stylesheets/extra.css`, or `mkdocs.yml` `theme.logo` / `theme.favicon` / `palette` /
  `font`.
- **Harness:** `mkdocs build`, then serve the built site (`mkdocs serve`, or any static server
  over `site/`), load a page in a browser, and screenshot it showing the custom font applied
  and the favicon in the tab. Confirm there are no 404s for `theme/*` or font assets in the
  network panel.
- **Artifact:** screenshot(s) under `evals/artifacts/<task-slug>/` showing the correct font and
  favicon; a note in `evals/<task-slug>.md` confirming no theme-asset 404s were observed.
- **Pass bar:** custom font visibly applied, favicon present, and zero 404s on `theme/*` and
  font assets.
- **Notes:** CONTRIBUTING.md is explicit that `--strict` does **not** validate the `@font-face`
  `url()`s or theme paths; a wrong path is a silent runtime 404. This row is the only thing
  that catches it. The font URLs in `extra.css` are `../theme/…` (relative to
  `docs/stylesheets/`); the `mkdocs.yml` paths are docs-root-relative `theme/…`.

## 4. Hover-preview demo
- **Trigger:** changes touching part-link previews: `{ data-preview }` attributes on
  Components-table links, the `material.extensions.preview` block in `mkdocs.yml`, or the
  `parts/**` include targets it points at.
- **Harness:** `mkdocs serve` (http://127.0.0.1:8000), open a board's Components page, hover a
  part-link row marked `{ data-preview }`, and capture the preview card.
- **Artifact:** a screenshot of the rendered preview card under `evals/artifacts/<task-slug>/`.
- **Pass bar:** hovering a `{ data-preview }` part link shows a preview card containing that
  part page's content (for example its Availability section).
- **Notes:** screenshot only (no gif). Preview targets are limited to `parts/**` per
  `mkdocs.yml`, so links to non-part pages will not preview.

## 5. Factual-data provenance (sourced / maintainer-gated)
- **Trigger:** any change to a factual value: compatibility, availability tokens (`Mode`,
  `Support`, `Have it Made`, `Third Party`, `Unavailable`), version (`Original`, `Updated`,
  `Standard`), production years, status (`Legacy`, `Current`), or sourcing, in any Components
  table, Availability card, or part chip.
- **Harness:** not machine-derivable from the repo. The value must come verbatim from the
  authoritative source spreadsheet in `references/` (copy `references/` into the worktree per
  the bootstrap). Where the spreadsheet is present, the published value must exact-match the
  source value; cite the matching source location in `evals/<task-slug>.md`. A value not found
  in the source is flagged for the maintainer, not invented.
- **Artifact:** a provenance note in `evals/<task-slug>.md` listing each changed fact and
  either "matches source" or an explicit "maintainer to confirm" flag. Do **not** paste raw
  spreadsheet contents or other unpublished `references/` data into the committed report
  (public repo).
- **Pass bar:** every changed fact either exact-matches the source spreadsheet or is flagged
  unresolved for maintainer confirmation. Nothing is inferred, derived from overlap, or
  fabricated. Forward/backward compatibility that is not stated explicitly in the source must
  be confirmed by the maintainer, not assumed.
- **Notes:** this is the repo's analog of a scored/gated tier. Facts are scored against an
  authoritative external source, never auto-asserted, and the pipeline must never set or
  fabricate a value to make a goal pass.

---

## No live / gated tier

This repo has no external write surface, so there is no live/gated evaluation tier and no
guardrail env var to set. The only deployment is GitHub Pages, performed by CI
(`.github/workflows/build.yml`) on pushes to `main`; the pipeline never deploys, publishes to
Pages, or writes to any external service. Merging an approved PR to `main` is what triggers a
deploy, through the normal CI path.
