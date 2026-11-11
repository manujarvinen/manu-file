# manu-format

A `.manu` file is a single HTML file that is both a document and its own editor: Markdown content, embedded images and fonts, and the app that edits, paginates and exports it — all in one file that works offline.

The project is at an early stage. Direction and structure are still open; treat what follows as the current starting point, not a fixed spec.

## Reference

`ref/initial_reference.html` (~900 KB, saved from gearnoodle.org/manu.manu) is the working reference implementation. Most of its size is base64 assets and fonts — search or slice it rather than reading it whole.

How the file is laid out:

- `<script type="text/manu">` — the document source, as Markdown. Images take a trailing attribute block, e.g. `![a.png](a.png){w=88mm h=41mm x=35 y=58 z=1.62 fit=cover align=right}`.
- `#manu-assets` — JSON map of file name → data URI.
- `#manu-settings` — JSON: `body`, `heading` (font themes), `view` (`digital` | `pages`).
- `#manu-fonts` — JSON of compressed embedded fonts.
- `#manu-style`, `#manu-app` — the CSS and the editor (~90 KB of plain ES5-style JS in one IIFE, no dependencies).

What the app does: block-based Markdown editing, drag reordering, image placement and optimisation, measured A4 pagination shared by the canvas preview and a hand-written true-text PDF exporter, self-serialising save (`.manu` / `.html`), and zip / Markdown / image import and export.

## Principles

- One file, no build-time or runtime dependencies, no network. It must keep working when opened from disk.
- The saved file must always reopen and stay editable — the save/load round trip is the core contract.
- The Markdown source stays readable and recoverable without the app.
- Document content is untrusted: keep link and HTML sanitising intact.

## Working here

- No build system, tests or tooling exist yet; introduce them only when they are needed, and keep any build output a single file.
- Verify changes in a real browser: edit, save, reopen, and check the PDF export.
- Treat `ref/` as a read-only reference; new work goes elsewhere in the repo.
