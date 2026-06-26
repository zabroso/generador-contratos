# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the app

No build step. Open `index.html` directly in a browser:

```bash
open index.html
```

`html2pdf.js` is loaded from CDN — an internet connection is required for PDF export.

## Architecture

Single self-contained file (`index.html`) with no framework, no bundler, and no package manager. Everything — styles, markup, and logic — lives in one file.

**Layout:** two-panel split. Left `#panel` holds the form; right `#preview-wrap` holds the live `#contract` document. They share the same DOM.

**Reactive pattern:** every `<input>`, `<textarea>`, and `<select>` fires `render()` on `input`/`change`. `render()` reads all field values and writes directly into the contract DOM via `innerHTML`. There is no state object — the inputs ARE the state.

**Key functions:**
- `render()` — the single source of truth; rebuilds the entire contract on every keystroke
- `ph(txt)` — returns a styled red placeholder `<span>` for unfilled fields
- `numALetras(n)` — converts a number to Spanish words (supports up to millions); used inline in the price clause
- `resetForm()` — clears only client/project fields; provider data (`p-*` inputs) is intentionally preserved
- `exportPDF()` — wraps `html2pdf()` around `#contract`; filename is `Contrato_{empresa}_{fecha}.pdf`

**Provider data** is hardcoded as `value="…"` attributes on the `p-*` inputs in the HTML — not in JS constants. To change the default provider, edit those `value` attributes directly.

## Gotchas

- The year `2026` is hardcoded in two places inside `render()` (subtitle and date formatting) and in `exportPDF()`. It is not derived from the system clock.
- `numALetras` is intentionally simplified: it handles integers only, no decimals, and stops at millions. Edge cases (e.g. 21, 100, 1000000) have been manually verified.
- The `@print` media query hides `#panel` and strips box-shadows — do not add layout changes that break print/PDF output.
- Chilean locale (`es-CL`) is used throughout for number formatting and dates.
