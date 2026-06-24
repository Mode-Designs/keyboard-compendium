# Keyboard Compendium

Open-source MkDocs (Material) reference site that helps Mode keyboard owners maintain
and customize their boards: parts, cross-compatibility, availability, design files, and
vetted community projects. It is a **factual reference**: accuracy matters more than
coverage. **The repo is public.**

## Working principles

**Think before coding.** State assumptions explicitly. If multiple interpretations exist,
surface them; don't pick silently. If a simpler approach exists, say so. When something
is unclear, stop, name what's confusing, and ask.

**Simplicity first.** The minimum that solves the problem: no speculative features,
no abstractions for single-use code, no flexibility that wasn't requested.

## Never invent part data

Compatibility, availability, sourcing, and version facts are authoritative data. They come
from the maintainer as a source spreadsheet kept under `references/` (gitignored).
**Never guess or fabricate them.** When a fact seems derivable from existing data (overlapping
usage, or forward/backward compatibility that isn't stated explicitly), **do not assume; ask
the maintainer to confirm.**

## Authoring content

All content follows the information hierarchy and conventions in **[CONTRIBUTING.md](CONTRIBUTING.md)**.
Read it before adding or editing pages. Adhere to it closely; deviate only for edge cases that
genuinely help users, and call out any deviation when you make it.

Quick reference (full detail in CONTRIBUTING.md):

- **Naming:** file/URL slugs are name-then-year (`sixtyfive-2021-plate-ansi.md`); displayed
  names are year-then-name ("2021 SixtyFive Plate / ANSI").
- **New part pages** start from `templates/part.md`. Strip the placeholder `#` links; they
  belong only in the template, never on a real page.
- **Files (you copy + rename):** working source is local-only under gitignored `references/`:
  CAD in `references/design_files/`, images in `references/drawings/<year-name>/`. Publish a
  copy into the **flat** homes renamed to the slug: CAD → `docs/files/`, images → `docs/assets/`
  as `.webp` (the standard). Shared parts = one canonical file referenced by many pages. Site
  chrome (logo, favicon, fonts) lives in `docs/theme/`, never `docs/assets/`.
- **Parts are not in the nav** (`not_in_nav` in `mkdocs.yml`); they're reached from each board's
  Components table. Adding a _board_ requires a new entry under `nav:` in `mkdocs.yml`.

## Done means a clean strict build

Before reporting a change complete, run `mkdocs build --strict` and confirm it passes. This
matches CI and catches broken links, missing files, and bad references. Use `mkdocs serve` for a
visual check whenever layout or rendering is involved.
