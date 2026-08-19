# Contributing to Nebulux

Thanks for taking an interest in the theme — bug reports, fixes and compatibility patches are all welcome.

This guide exists mostly for one reason: Nebulux routes all of its accent colors through a chain of CSS variables, and a change that looks correct in isolation can silently blank out half the interface. The [Color variable contract](#color-variable-contract) section is the part worth reading before you touch `theme.css`.

---

## Quick checklist

Before opening a pull request:

- [ ] Tested in a real vault, in **both** reading view and editor (live preview)
- [ ] Tested with **custom** callouts (`nav`, `status`, `projects`) **and** native ones (`info`, `warning`, `danger`, `quote`)
- [ ] `grep -E 'rgba?\(\s*var\(' theme.css` returns nothing
- [ ] No network requests added (fonts and images must be embedded)
- [ ] `manifest.json` version left untouched — the maintainer bumps it at release time
- [ ] Before/after screenshots in the PR description for anything visual

---

## Project layout

```
theme.css        the entire theme — ~1340 lines, 9 numbered sections
manifest.json    name, version, minAppVersion
CHANGELOG.md     release notes (machine-read, see below)
screenshot.png   gallery screenshot
```

**There is no build step and no dependencies.** What you edit is exactly what ships to users. `theme.css` is organised into nine commented sections:

| # | Section |
| - | ------- |
| 1 | Global & typography |
| 2 | System interface (opaque & neon) |
| 3 | Modules: callouts & dashboard |
| 4 | Interactive elements (scroll, tags, toggles, checkboxes) |
| 5 | Separators & graph view |
| 6 | File explorer (dynamic neon nebula) |
| 7 | Dataview (dynamic links) |
| 8 | Highlights — nebula effect |
| 9 | Nebulux tools (patches & alignment fixes) |

Keep your change inside the section it belongs to.

---

## Local setup

Point a test vault at your working copy:

```bash
# from your vault
mkdir -p .obsidian/themes/Nebulux
cp /path/to/Nebulux/theme.css /path/to/Nebulux/manifest.json .obsidian/themes/Nebulux/
```

A symlink works too and saves you the copy on every edit. Then `Settings → Appearance → Nebulux`, and reload with `Ctrl+Shift+R` after each change.

Install the **Style Settings** plugin as well — a good part of the theme is exposed through it, and it is easy to break without noticing.

---

## Color variable contract

**Every color variable in this theme holds a complete CSS color — never a bare RGB triplet.**

```css
/* ✅ */                              /* ❌ */
--color-nav: rgb(120, 180, 255);      --color-nav: 120, 180, 255;
--color-nav: #78b4ff;
```

This is not a style preference. Since Obsidian 1.13, `--callout-color` is provided as a full color value, and the theme's variables feed into it. Mixing the two conventions produces `rgb(rgb(...))` or `color: 120, 180, 255` — both invalid at computed-value time, which means the browser **drops the whole declaration silently**. Nothing errors; the color just disappears.

### The chain

Accents are not set directly on elements. They travel through four hops:

```
--theme-nav-rgb        Style Settings (user's picker)
    ↓
--color-nav            :root — the theme's source of truth
    ↓
--hud-color            per-callout accent
    ↓
--callout-color        what Obsidian itself reads
```

There are three parallel chains — `nav` (blue), `status` (gold), `projects` (bronze) — plus `--color-cross`.

**If you change one hop, check the others.** Converting only the last hop leaves the values upstream as triplets and blanks out folder colors, tags, callout titles, checkboxes, `<hr>` separators and table headers. Converting only the root breaks every native callout instead. This exact mistake is documented in [PR #1](https://github.com/sikoso774/Nebulux/pull/1) if you want the full story.

### Applying transparency

Since the variables hold complete colors, `rgba(var(--x), 0.5)` cannot work. Use `color-mix()`:

```css
/* ✅ */
background: color-mix(in srgb, var(--color-nav) 15%, transparent);
border: 1px solid color-mix(in srgb, var(--hud-color, #a0bedc) 50%, transparent);

/* ❌ */
background: rgba(var(--color-nav), 0.15);
```

### The sweep

One command catches every regression of this kind:

```bash
grep -E 'rgba?\(\s*var\(' theme.css
```

**It must return nothing.** Any hit means a triplet-style variable is still in play somewhere.

---

## House style

- **Prefix selectors with `body`.** Around 230 rules do this to win specificity against Obsidian's own styles without resorting to `!important`.
- **Style both views.** Reading view (`.markdown-rendered`) and editor (`.cm-*`) are separate DOM trees. A change to one usually needs its counterpart — the theme has ~31 and ~29 rules respectively.
- **Keep the `@settings` block in sync.** The YAML block at the top of `theme.css` declares 26 Style Settings entries. If you add, rename or remove a user-facing variable, update it there too, defaults included.
- **Dark only for now.** `.theme-light` deliberately mirrors the dark palette with `color-scheme: dark`. A real light theme is planned for v2.0 — please don't add partial light support in the meantime.
- **Mind the contrast.** The theme targets WCAG AA (4.5:1 for text, 3:1 for non-text). Check any color you change.

---

## Obsidian platform rules

These are enforced by the Obsidian review process, not by preference:

- **No network requests.** No CDN fonts, no remote images, no `@import` from a URL. Fonts and images are embedded as base64 data URIs directly in `theme.css`.
- **No obfuscated code, no telemetry, no self-update mechanism.**
- Keep an eye on file size when embedding — `theme.css` currently sits at ~52 KB.

---

## Changelog and versioning

**Don't bump `manifest.json` in a pull request.** Pushing to `main` triggers an automated release: the workflow reads the version, and if no matching tag exists it creates the tag, extracts the notes from `CHANGELOG.md` and publishes the GitHub release. A version bump in a PR either fires early or collides with the maintainer's own bump.

If your change deserves a changelog entry, describe it in the PR description rather than editing `CHANGELOG.md` — the maintainer folds it into the next release entry.

The changelog format is **parsed by the release workflow**, so it has to stay exactly:

```markdown
## [1.1.8] - 2026-08-19 — Short title

Intro paragraph.

### 🛠️ Fixed — What was fixed

Explanation of the cause, not just the symptom.
```

The workflow reads everything between one `## [` heading and the next. Explain *why* something broke — past entries are written that way on purpose, and they end up as the public release notes.

---

## Opening a pull request

1. Branch from `main`.
2. Keep the change focused — one fix per PR.
3. Describe the symptom, the cause, and the fix. Screenshots before/after for anything visual.
4. Say which Obsidian version you tested on.

Automated compatibility patches are welcome, but please run them against a real vault before opening the PR. A pattern that is correct for most themes may only be half-correct here, precisely because of the variable chain described above.

---

*Questions? Open an issue — happy to help.*
