# Mode Keyboard Compendium

This repository exists to help you keep Mode keyboards on the desk for as long as possible.

Here you'll find:

- Information about legacy and current boards
- Replacement part details and compatibility notes
- CAD files for certain parts, where appropriate
- Links to common third-party parts where they make sense
- A directory of vetted community projects and resources

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

## License

Design files and content are for personal, non-commercial use only. See [LICENSE.md](LICENSE.md).
