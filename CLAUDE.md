# manu-format

A `.manu` file is a single HTML file that is both a document and its own editor: Markdown content, embedded images and fonts, and the app that edits, paginates and exports it — all in one file that works offline.

The project is at an early stage. Direction and structure are still open; treat what follows as the current starting point, not a fixed spec.

## Files

`manu-format.html` (~330 KB) is the working source — the format's app and its welcome document in one file. Edit it directly.

It was derived from a browser "save page" copy of gearnoodle.org/manu.manu, which is no longer kept in the repo (it remains in git history).

Most of the file is base64 assets and fonts — search or slice rather than reading it whole.

The UI chrome is a small accent-coloured island fixed top-left (`#island`): the MANU logo on top (click: About MANU), and below it a fullscreen-preview button and a menu button that holds every other command. There is no top bar.

How the file is laid out:

- `<script type="text/manu">` — the document source, as Markdown. Images take a trailing attribute block, e.g. `![a.png](a.png){w=88mm h=41mm x=35 y=58 z=1.62 fit=cover align=right}`.
- `#manu-assets` — JSON map of file name → data URI.
- `#manu-settings` — JSON: `body`, `heading` (font themes), `view` (`digital` | `pages`).
- `#manu-fonts` — JSON of compressed embedded fonts.
- `#manu-style`, `#manu-app` — the CSS and the editor (~90 KB of plain ES5-style JS in one IIFE, no dependencies).

Consecutive image blocks with different `align` values (left, centre, right) share one line when their widths fit the column (`layoutRows`); they remain separate blocks.

Undo/redo is recorded automatically inside `render()` (`histRecord`): any change to the Markdown, assets or fonts that reaches a render becomes a step, so new features need no snapshot calls — just mutate `S` and render. View, zoom and scroll are deliberately not recorded.

What the app does: block-based Markdown editing, drag reordering, image placement and optimisation, measured A4 pagination shared by the canvas preview and a hand-written true-text PDF exporter, self-serialising save (`.manu` / `.html`), and zip / Markdown / image import and export.

## Principles

- One file, no build-time or runtime dependencies, no network. It must keep working when opened from disk.
- The saved file must always reopen and stay editable — the save/load round trip is the core contract.
- The in-page stores (`text/manu`, `#manu-assets`, `#manu-settings`) are kept in sync with the live document on every render, so even the browser's own "save page" carries current content. Ctrl+S is intercepted and writes the proper single file.
- The Markdown source stays readable and recoverable without the app.
- Document content is untrusted: keep link and HTML sanitising intact.

## Versions

The app's version is the `VERSION` constant in `#manu-app` (next to `aboutModal`); the "About MANU" menu entry shows it. The product name is written MANU in the UI.

- Every commit that changes `manu-format.html` bumps `VERSION` (semver: patch for fixes, minor for features) and is tagged `v<VERSION>`.
- Keep `VERSION`, the tag and the About MANU dialog in agreement; never tag without bumping.

## Working here

- No build system, tests or tooling exist yet; introduce them only when they are needed, and keep any build output a single file.
- Verify changes in a real browser: edit, save, reopen, and check the PDF export.
