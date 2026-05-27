# Changelog

All notable changes to this project will be documented in this file.

---

## [1.1.3] - 2026-05-27 — Font De-embedding (Size Reduction)

Removed the 5 embedded Montserrat `@font-face` variants (400, 400i, 500, 600, 700) to bring `theme.css` under the Obsidian community size threshold.

### ⚡ Performance — Montserrat De-embedded

Reduced `theme.css` from **108 KB to ~40 KB** (-63%) by removing the Montserrat WOFF2 base64 blobs. Orbitron (headers) remains embedded. Montserrat is the default text font but must be installed locally; it can also be swapped for any other font via the **Style Settings** plugin.

---

## [1.1.2] - 2026-05-27 — Compatibility & Bot Review Fixes

Targeted fixes addressing warnings raised by the Obsidian community bot review.

### ♿ Fixed — WCAG Color Palette (Obsidian Bot Warning)

Added the full `--color-base-00` → `--color-base-100` color spectrum to `.theme-dark` and `.theme-light`. The bot was computing a contrast ratio of 1.6:1 from the background gradient variables alone (`#1a253a` vs `#050505`) — the palette now anchors properly from `#000000` to `#e0e6ed`, reaching a 16.7:1 ratio.

Also added `--color-accent`, `--interactive-accent`, and `--text-on-accent` to align with Obsidian's design system.

### 🛠️ Fixed — `text-decoration` Partial Support (Obsidian 1.4.5)

Replaced the CSS Level 4 shorthand `text-decoration: underline rgb(...)` (not supported in Obsidian's embedded Chromium) with two explicit properties:

- `text-decoration-line: underline`
- `text-decoration-color: rgb(var(--hud-color))`

Affects the neon underline on callout table links on hover. Visual result is identical.

### 🔧 Fixed — Active Tab Specificity Override

Resolved a CSS specificity conflict where Obsidian's `app.css` rule at specificity `(0,6,1)` was overriding the active tab text and icon colors. The selector chain was extended to match the same specificity level — since `theme.css` loads after `app.css`, it now correctly takes precedence. Active tab title and icon now render in `--color-nav` (neon blue) with glow.

### ⚡ Performance — Font Subsetting (Latin only)

Reduced `theme.css` from **166 KB to 109 KB** (-35%) by subsetting the embedded WOFF2 fonts (Orbitron + Montserrat) to the Latin Unicode range (`U+0020-024F` + common symbols). All 6 font variants are preserved; only unused glyphs are stripped. Resolves the HTTP 413 error on the Obsidian community portal screenshot service.

---

## [1.1.1] - 2026-05-26 — Accessibility Patch (WCAG AA)

A targeted accessibility patch to improve color contrast compliance.

## ♿ Accessibility Fixes (WCAG AA)

The default gradient end colors for headers H2, H4, H5, and H6 were too dark against the theme's deep space background, resulting in contrast ratios below the WCAG AA minimum (4.5:1 for normal text, 3:1 for large text).

All four have been updated to lighter variants that maintain the gradient aesthetic while meeting accessibility standards:

| Header | Before    | After     | Contrast (before) | Contrast (after) |
| ------ | --------- | --------- | ----------------- | ---------------- |
| H2 end | `#2563eb` | `#5b9cf6` | ~2.97:1 ❌ | ~5.49:1 ✅ |
| H4 end | `#b91c1c` | `#eb6060` | ~2.37:1 ❌ | ~4.68:1 ✅ |
| H5 end | `#6d28d9` | `#b06ef5` | ~2.16:1 ❌ | ~4.67:1 ✅ |
| H6 end | `#475569` | `#789ab5` | ~2.02:1 ❌ | ~5.18:1 ✅ |

> Ratios measured against the gradient center background `#1a253a` (worst case).

These are **default values only** — if you have already customized your header gradient colors via Style Settings, your personal values are preserved.

---

*Small fix, big impact. Thank you for exploring the cosmos with Nebulux!* 🌠

---

## [1.1.0] - 2026-05-11 — Massive "!important" Reduction & Neon Graph View

A major architectural milestone. The CSS engine has been completely overhauled for a more robust and compatible experience, alongside a highly requested visual feature.

## ✨ What's New

### 🌌 The Neon Graph View
We've brought the Sci-Fi aesthetic directly into your knowledge graph!
By implementing a global WebGL canvas `drop-shadow` hack, your nodes and connection lines now emit a stunning "Neon" glow against the deep space background. The best part? This effect is completely CSS-based and **100% compatible with complex Canvas/WebGL plugins like Excalidraw**.

## 🛠️ Under the Hood (Architecture Refactoring)

### 🧹 Massive `!important` Reduction
We've achieved a much cleaner CSS architecture! The entire codebase has been meticulously refactored to drastically reduce the number of `!important` tags.
- **What this means for you:** Nebulux now relies on proper DOM hierarchy and CSS specificity (`body`, `.theme-dark`). This makes the theme incredibly friendly to use alongside your personal CSS snippets. You can now easily override any Nebulux color or property without having to fight against forced styles!
- **Plugin Compatibility:** Flawless integration with community plugins and the Obsidian core engine.

### 📐 Precision Alignments & Typography Fixes
- **Checkboxes:** Rebuilt the `task-list-item-checkbox` layout. Checkboxes are now perfectly centered vertically, and multi-line tasks will flawlessly wrap and indent in both *Live Preview* and *Reading View* without breaking the alignment.
- **Reading View Headers:** Fixed a specificity bug where custom typography (like *Orbitron* or *Rajdhani*) would disappear on H1-H6 headers when switching to Reading Mode.

---

*Thank you for exploring the cosmos with Nebulux! As always, feel free to report any bugs or suggest features on the GitHub repository.* 🌠

---

## [1.0.0] - 2026-05-07 — Initial Release

Very first official release of **Nebulux** (formerly Galaxy Theme), marking a massive overhaul to meet the high standards of the Obsidian community.

## 🌟 What's New in v1.0.0

### 1. Rebranding & Identity
* **New Name:** Officially rebranded from "Galaxy Master HUD" to **Nebulux**.
* **Source Code Aesthetics:** Injected a massive ASCII Art signature in `theme.css`.
* **Internationalization:** Fully translated the `manifest.json`, CSS comments, and *Style Settings* options into English to support the global Obsidian community.

### 2. Features Included in the Core Theme
* **Sci-Fi Cockpit Design:** Darkened background with a radial gradient, giving an immersive spaceship dashboard vibe.
* **Dynamic Titles:** Custom gradient text for headers (H1 to H6) using the futuristic *Orbitron* font.
* **The 3 Pillars (Callouts):** Unique custom callouts designed for dashboards:
  * `> [!nav]` (Neon Blue)
  * `> [!status]` (Tactical Gold)
  * `> [!projects]` (Tactical Bronze)
* **Dashboard Support:** Two methods for building dashboards:
  * Flexbox menus using `> [!nav/menu]`
  * Full-page grids using the `dashboard` cssclass.
* **Glassmorphism:** Elegant blur effects on floating menus, modals, and the application ribbon.
* **Rainbow Folders:** Automatic neon colorization for files and folders in the file explorer.

### 3. Bug Fixes & Improvements
* **Typography Fix:** Removed illegal `!important` tags from CSS variables to ensure the **Montserrat** font loads and displays flawlessly across the application.
* **Unified Checkboxes:** Overhauled markdown checkboxes to display perfectly in both Source mode and Reading mode.

### 4. Documentation
* **Super README:** Completely rewritten `README.md` featuring HTML alignment, Shields.io dynamic badges, and comprehensive documentation for Callouts and Dashboards.

---

### 📦 How to install
Since this is the initial release, users can download the `theme.css` and `manifest.json` from the assets below, or install it directly via the Obsidian Community Themes gallery once approved.

*Designed & Coded with ✨ by [@Sikoso774](https://github.com/Sikoso774)*
