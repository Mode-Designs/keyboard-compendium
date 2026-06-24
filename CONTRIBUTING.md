# Contributing & Authoring Guide

This is the canonical spec for the Keyboard Compendium's structure, vocabularies, naming, and
file organization. It serves human contributors and AI assistants alike. Follow it closely; deviate
only for edge cases that genuinely help users, and call the deviation out in your PR.

> **The compendium is a factual reference.** Never invent compatibility, availability, sourcing,
> or version facts; those come from the maintainer or the source spreadsheet. If a fact seems
> derivable from existing data (overlapping parts, unstated forward/backward compatibility), ask
> the maintainer to confirm rather than assuming.

## Local setup & build

```sh
pip install -r requirements.txt
mkdocs serve            # local preview at http://127.0.0.1:8000
mkdocs build --strict   # must pass before a change is "done" (this is what CI runs)
```

`--strict` fails on broken links, missing files, and bad references. A change isn't complete
until it passes.

## Repository layout

| Path | Role |
|:---|:---|
| `docs/` | Everything the site publishes (MkDocs source root). The **only** tracked content. |
| `docs/keyboards/<name-year>/` | One folder per board (the four pages below) |
| `docs/parts/` | Flat part pages, **not** in the nav; reached from Components tables |
| `docs/files/` | Published CAD downloads, **flat**, slug-named `.dxf`/`.step`. Content only. |
| `docs/assets/` | Published images, **flat**, slug-named `.webp`. Content only, **no chrome**. |
| `docs/theme/` | Site chrome: `logo.svg`, `favicon.png`, fonts. Fixed paths, not slug content. |
| `templates/part.md` | Starting point for new part pages |
| `references/` (gitignored) | **Working source, local-only.** Holds the data spreadsheet plus `references/design_files/` (raw CAD `dxf-…`/`step-…`) and `references/drawings/<year-name>/` (source `.webp`). Never published or tracked. |
| `mkdocs.yml` | Site config + nav (boards are listed here; parts are excluded) |

The working source lives in gitignored `references/` and is local-only; it is **not** part of
the public repo. Content is live only once it has been published into `docs/` (and, for a board,
given a `nav:` entry). Publishing is a copy-and-rename out of `references/` into the flat `docs/`
homes.

## Naming conventions

- **File names and URL slugs** are **name-then-year**: `sixtyfive-2021-plate-ansi.md`
- **Displayed names** (page titles, tags, chips, table cells, prose) are **year-then-name**:
  "2021 SixtyFive Plate / ANSI"
- The working-source folders use the display order (`references/drawings/2021-sixtyfive/…`);
  publishing **reorders to the slug form**.
- All published slugs are **lowercase** (`a-z0-9-` only); no spaces.

## File organization

**Shared / cross-board parts.** A part used by more than one board (shared 65% parts, model-named
PCBs like `pcb-m80h-v2`, accessories like `plate-caps`) is published as **one** canonical flat
file, referenced from every board's Components table that uses it. Never duplicate it per board.
If two same-named source copies are **not** byte-identical they are not the same part; give them
distinct slugs rather than collapsing.

**Site chrome** (`logo.svg`, `favicon.png`, fonts) lives in `docs/theme/`, never in `docs/assets/`.
It is referenced at fixed paths: `mkdocs.yml` `theme.logo`/`theme.favicon` (docs-root-relative,
`theme/…`) and the `@font-face` `url()`s in `docs/stylesheets/extra.css` (`../theme/…`). The font
URLs are **not** validated by `--strict`: a wrong path is a silent runtime 404, so verify fonts
and favicon on the built site after any chrome change.

## Information hierarchy

### A board = a folder with four pages

`docs/keyboards/<name-year>/` contains exactly:

1. `index.md`: overview / hub
2. `components.md`: the parts matrix
3. `design-files.md`: downloadable CAD, aggregated
4. `community-projects.md`: vetted community work

Every page begins with a frontmatter `title:` set to the display name.

#### `index.md`

```
---
title: 2021 SixtyFive
---

`Status: Legacy` · `Production Years: 2021-2022` · `Layout: 65%`

![2021 SixtyFive](../../assets/sixtyfive-2021.webp)

<plain, factual intro: what the board is, when it was made, defining traits. No marketing.>

## [:material-link: Components](components.md)
<one-line description>

## [:material-link: Design Files](design-files.md)
<one-line description>

## [:material-link: Community Projects](community-projects.md)
<one-line description>
```

Status chips, in this order, rendered as inline code spans joined by ` · `:

| Chip | Values |
|:---|:---|
| `Status` | `Legacy` (out of production) · `Current` (in production) |
| `Production Years` | e.g. `2021-2022`, `2024-present` |
| `Layout` | e.g. `65%`, `75%`, `TKL`, `Alice` |

#### `components.md`

- `title:` frontmatter (display name + " Components").
- A short, factual intro (e.g. note legacy status and what that means for sourcing).
- A link to the shared **[Conventions](../../index.md#conventions)** section for Version and
  Availability terms; **do not** re-table the terminology on each board.
- One `##` section per component category, in the **canonical order** below. Omit categories the
  board doesn't have; do not invent new ones without maintainer sign-off.
- Each section is a three-column table:

  ```
  | Compatible Parts | Version | Availability |
  |:---|:---|:---|
  | 2021 SixtyFive Plate / ANSI | Original | `Mode`  `Have it Made` |
  ```

- A part that has its own page links its name to `../../parts/<slug>.md#availability` with
  `{ data-preview }` (enables the hover preview). Parts without a page are plain text.
- The `Version` column uses the Version vocabulary; the `Availability` column uses Availability
  tokens as inline code spans (multiple allowed, space-separated).

**Canonical component-category order**

1. **Case**: Top Case, Bottom Case, Accent
2. **Plate & Mounting**: Plate(s), Plate Caps, gaskets / mount hardware
3. **Electronics**: PCB(s), Daughterboard, JST Cable
4. **Acoustics**: Silicone Base, Plate Foam, Case Foam, PE Foam
5. **Feet**
6. **Fasteners**: Case, Top Mount, Plate / Hotswap, Daughterboard, Silicone Base

#### `design-files.md`

- `title:` frontmatter.
- The non-commercial admonition (copy from an existing board):

  ```
  !!! info "Not for commercial use"

      These files are intended for personal use only. You may work with shops to have parts
      produced, but they may not be used to produce or market parts with Mode branding or
      implying any association with Mode.
  ```

- Downloads grouped by `###` subsection (Plates, Case, Accessories, …). This page is the
  **aggregate** of every downloadable file for the board; individual part pages link to their
  own subset. Both point at the same files in `docs/files/`.

#### `community-projects.md`

- `title:` frontmatter.
- When populated, a list of **vetted** projects: name, author/source, link, one-line description.
- When empty, a short placeholder plus the "open an issue or PR" invitation.

### A part = one file in `docs/parts/`

Start from `templates/part.md`. Structure:

```
---
title: 2021 SixtyFive Plate / ANSI
tags:
  - 2021 SixtyFive
---

`2021 SixtyFive`

![2021 SixtyFive Plate / ANSI](../assets/sixtyfive-2021-plate-ansi.webp)

## Availability        <!-- grid of cards; include ONLY the cards that apply -->
## Design Files        <!-- this part's downloads -->
## Compatible Replacements   <!-- cross-references to other part pages -->
```

Rules:

- The board chip is a single inline code span with the board's display name.
- **Availability cards must mirror the Availability tokens shown for this part in the Components
  table**: same set, no more, no less. No placeholder `#` links on a real page.
- **Compatible Replacements** are cross-references (including cross-board forward/backward
  compatibility). The Components table is the source of truth for compatibility *within* a board.

### Which components get their own part page?

Give a component a dedicated page when it has **any** of: design files to download, more than one
sourcing option, or compatibility nuance worth explaining. Trivial standard parts (e.g. generic
M2 fasteners) stay as a Components table row only.

## Controlled vocabularies

These live in the homepage's **Conventions** section (`docs/index.md#conventions`) and must be
used verbatim.

**Version**

| Term | Meaning |
|:---|:---|
| Original | The original design at release |
| Updated | An updated but compatible part |
| Standard | A common part available outside of Mode |

**Availability** (rendered as inline code spans)

| Token | Meaning |
|:---|:---|
| `Mode` | Available for purchase directly from the Mode online store |
| `Support` | Available by contacting support@modedesigns.com |
| `Have it Made` | Design files available for manufacturing at a shop (see Design Files) |
| `Third Party` | Available from third-party vendors |
| `Unavailable` | The part cannot be made or purchased |

## Checklists

**Add a new board**

1. Create `docs/keyboards/<name-year>/` with `index.md`, `components.md`, `design-files.md`,
   `community-projects.md` (use 2021 SixtyFive as the reference implementation).
2. Publish the hero + part images to `docs/assets/` as `.webp` (copy + rename from `drawings/`).
3. Fill the Components matrix from maintainer-provided data; never invent facts.
4. Create part pages (from `templates/part.md`) for components that warrant one; publish their
   CAD to `docs/files/`.
5. Add the board to `nav:` in `mkdocs.yml`.
6. `mkdocs build --strict` passes.

**Add or update a part page**

1. Copy `templates/part.md` → `docs/parts/<slug>.md`; strip placeholder `#` links.
2. Publish image (`docs/assets/*.webp`) and any CAD (`docs/files/*`).
3. Availability cards match the Components-table tokens; Compatible Replacements link real pages.
4. Link the part from the relevant Components table row(s) with `#availability` + `{ data-preview }`.
5. `mkdocs build --strict` passes.

## Submitting changes

Open an issue or pull request. Keep `mkdocs build --strict` green. For data questions or to
report an error, you can also reach support@modedesigns.com.
