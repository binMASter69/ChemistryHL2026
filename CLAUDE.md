# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A static IB Chemistry HL study guide website for ACSI (Anglo-Chinese School International), 2026 cohort. There is no build system, no package manager, and no test suite — both files are self-contained HTML pages that open directly in a browser.

## Files

- **`index.html`** — Main Organic Chemistry HL study guide. Dark-themed SPA using vanilla JS.
- **`Analytical Spectroscopy & Isomer Sandbox.htm`** — Interactive isomerism and spectroscopy tool. Light-themed, ACSI navy/red/gold branding.

## Running Locally

```bash
# Any static server works; e.g.:
python3 -m http.server 8080
# Then open http://localhost:8080
```

The `.htm` file's name contains spaces and an ampersand — quote it in the shell:

```bash
open "Analytical Spectroscopy & Isomer Sandbox.htm"
```

## Architecture: `index.html`

Single HTML file with all CSS and JS inline. No external dependencies except Google Fonts (Playfair Display, DM Mono, DM Sans).

**Navigation pattern:** A sticky `<nav>` with `.nav-btn` elements calls `show(id)`. That function removes `.active` from all `.section` elements and adds it to the target section, then scrolls to top. Only one section is visible at a time.

**9 sections** (nav IDs): `rxnmap`, `reagents`, `mechanisms`, `sn`, `aromatic`, `isomerism`, `spectra`, `tests`, `quiz`.

**Quiz logic:** An `answered` object prevents re-answering. Correct/wrong states are applied as CSS classes (`.correct`, `.wrong`). The `explanations` object (keyed 1–8) holds feedback strings. Score is updated in the DOM directly.

**CSS design tokens** in `:root`:
- `--bg: #0d0f14`, `--accent: #5ee7b0` (green), `--accent2: #f7a94b` (orange), `--accent3: #7c9fff` (blue), `--accent4: #f76e8a` (pink)

## Architecture: `Analytical Spectroscopy & Isomer Sandbox.htm`

Depends on the CDN-hosted **smiles-drawer@1.0.10** library (`cdn.jsdelivr.net`) to render 2D molecular structures from SMILES strings onto `<canvas>` elements.

**Data model:** A `masterIsomerData` object is keyed by compound class (e.g. `alkene`, `cycloalkane`, `alcohol`, `ether`, `aldehyde`, `ketone`, `acid`, `ester`), then by carbon count `n` (1–6). Each entry has `{ name, smiles, nmr, ir }`.

**Isomeric pair selector:** The `<select id="isomerPair">` maps option values like `"alkene_cycloalkane"` to two compound-class keys split on `"_"`. The carbon slider (`<input type="range" id="carbonSlider">`) filters entries by carbon count. Changing either control calls the render function, which clears both grid panels and re-populates them with isomer cards.

**Rendering:** Each isomer card gets a `<canvas>` that `SmilesDrawer.apply()` targets using the `data-smiles` attribute.

## Content Conventions

- Chemical equations use Unicode subscripts/superscripts inline in HTML (e.g. `CH₃CH₂COOH`, `NO₂⁺`).
- IR wavenumbers are in cm⁻¹; NMR shifts in ppm (δ).
- SMILES strings in the sandbox follow standard Daylight notation (e.g. `"CC(=O)C"` for propanone).
- NMR environment descriptions use the format `"N Envs (Ratio a:b:c)"`.
