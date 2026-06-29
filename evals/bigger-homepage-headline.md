# Eval: bigger-homepage-headline

Enlarge the compendium homepage headline ("Mode Keyboard Compendium") to a hero size,
roughly 2x its previous size, without enlarging headings on dense reference pages.

## Change

- `docs/index.md`: tagged the homepage H1 with an `attr_list` class hook:
  `# Mode Keyboard Compendium { .hero-headline }`. `attr_list` is already enabled in
  `mkdocs.yml`, so this adds `class="hero-headline"` to that one H1 and nothing else.
- `docs/stylesheets/extra.css`: added a homepage-only rule
  `.md-typeset h1.hero-headline { font-size: clamp(3.25rem, 7vw, 5rem); line-height: 1.05; }`.
  The site-wide `.md-typeset h1` rule is left at `font-size: 2.75em`, so every other page's
  H1 (board titles, part titles, "Components" / "Design Files" / "Community Projects") is
  unchanged.

### Why a class hook and not a pure-CSS selector

MkDocs Material emits no homepage-specific signal in the rendered DOM (the `<html>`,
`<body>`, `<main>`, and `<article>` tags are byte-identical between the homepage and a board
page), so there is no CSS-only selector that targets the homepage alone. Every non-homepage
page also renders a real H1 synthesized from its `title:` front matter, so a global
`.md-typeset h1` bump would have enlarged all ~190 board/part/Components titles. The one-token
`attr_list` class on the homepage H1 is the minimal, theme-native way to scope the change; the
substantive styling stays in `extra.css`. This is a small, called-out deviation from a
literally CSS-only edit, taken because the goal's "scope to the homepage" and "don't regress
dense pages" criteria cannot both be met by CSS alone here.

### Size

Previous: `2.75em` against a `.md-typeset` base of `.65rem` ~= `1.79rem` rendered.
New hero: `clamp(3.25rem, 7vw, 5rem)`; at a 1400px viewport the `7vw` term clamps to the
`5rem` max ~= **2.8x** the previous size, landing at the ~5rem the existing CSS comment
already anticipated. The `clamp()` floor (3.25rem, ~1.8x) only engages below ~743px viewport
width, keeping the hero from overflowing on mobile.

## Row 1 - Strict build (check gate)

```
$ mkdocs build --strict
INFO    -  Cleaning site directory
INFO    -  Building documentation to directory: .../site
INFO    -  Documentation built in 0.99 seconds
exit: 0
```

**PASS** (exit 0).

Scope verified in the built site: exactly one rendered page contains `hero-headline`
(`site/index.html`); the homepage H1 is `<h1 class="hero-headline" ...>` while
`keyboards/encore-2026/components/index.html` renders a plain `<h1>Components`.

## Row 2 - Page render demo

Captured at 1400x1000 with headless Chrome against `mkdocs serve` (127.0.0.1:8000).

| Artifact | Shows |
|---|---|
| `artifacts/bigger-homepage-headline/before-homepage.png` | Homepage headline at the old ~2.75em (single line) |
| `artifacts/bigger-homepage-headline/after-homepage.png` | Homepage headline at the new hero size (wraps to two lines, ~2.8x) |
| `artifacts/bigger-homepage-headline/before-components.png` | Components page before |
| `artifacts/bigger-homepage-headline/after-components.png` | Components page after - identical; "Components" H1 and tables unchanged |

**PASS.** Homepage headline is visibly ~2x+ larger with a hero feel; the Components page
(representative dense reference page) is unchanged, confirming no regression. The before/after
Components PNGs are byte-identical.

## Provenance

No factual data (compatibility, availability, version, status, sourcing) was touched; row 5
does not apply. Change is presentation-only.
