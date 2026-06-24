# Mode Keyboard Compendium

This repository exists to help you keep Mode keyboards on the desk for as long as possible.

Here you'll find:

- Information about legacy and current boards
- Replacement part details and compatibility notes
- CAD files for certain parts, where appropriate
- Links to common third-party parts where they make sense
- A directory of community projects and resources

If you're not sure where to start:

1. Find your board under `docs/keyboards/`.
2. Open its Components page to see the parts list and compatibility.
3. Follow the links to the part pages in `docs/parts/` for more detail, CAD, and sourcing options.

If you have feedback or find an error, please open an issue or pull request.

## Development

The site is built with [MkDocs](https://www.mkdocs.org) and [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

```sh
pip install -r requirements.txt
mkdocs serve
```

## Conventions

- **File names and URL slugs** are name-then-year: `sixtyfive-2021-plate-ansi.md`
- **Displayed names** (page titles, tags, tables, prose) are year-then-name: "2021 SixtyFive Plate / ANSI"
- New part pages start from `templates/part.md`

See [CONTRIBUTING.md](CONTRIBUTING.md) for the full authoring guide: information hierarchy, controlled vocabularies, and file organization.

## License

This repository is multi-licensed: Mode's design files (CAD) under **CERN-OHL-P-2.0**, documentation and images under **CC BY 4.0**, and site code under **MIT** (full texts in [`LICENSES/`](LICENSES/)). You're free to make parts for yourself or commercially, but don't put Mode branding on them or imply Mode made them. "Mode" and the product names are trademarks of Mode Designs LLC. Everything is provided as is, with no warranty. See [LICENSE.md](LICENSE.md) for the full terms.
