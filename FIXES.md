# FIXES & AMÉLIORATIONS — Nebulux Theme
> Session du 2026-05-28 — Corrections appliquées sur `theme.css`

---

## 1. Isolation des callouts custom (SVG mask-image cassé par les dégradés H1-H6)

**Problème**
L'ajout des dégradés CSS (`background-clip: text; color: transparent`) sur les sélecteurs `h1`–`h6` dans `.markdown-rendered` créait un problème de compositing dans Chromium/Electron. Les `mask-image` des pseudo-éléments `::before` des icônes de callout étaient ignorés, affichant un rectangle coloré plein au lieu de l'icône SVG.

**Cause**
`-webkit-background-clip: text` sur un élément crée un contexte de compositing qui interfère avec les opérations `mask-image` sur les éléments voisins dans Obsidian (Electron/Chromium).

**Fix appliqué**
```css
body .callout {
    isolation: isolate; /* Crée un contexte de compositing indépendant */
}
```

---

## 2. Icônes emoji invalides dans les sous-types de callout

**Problème**
Les variantes de callout (`/brain`, `/idea`, etc.) définissaient `--hud-icon` comme une chaîne de caractères (ex: `"🧠"`). Or, `mask-image` n'accepte que des valeurs de type `url()` — la chaîne était ignorée silencieusement, affichant un rectangle coloré plein.

**Fix appliqué**
Remplacement de toutes les chaînes emoji par des SVG Lucide encodés en data URI `url("data:image/svg+xml,...")`, compatibles avec l'approche `mask-image` existante. Les icônes héritent désormais automatiquement de `--hud-color` (couleur du type parent).

**Sous-types convertis**
| Sous-type | Icône Lucide | Ancien emoji |
|-----------|-------------|--------------|
| `/idea`   | lightbulb   | 💡 |
| `/brain`  | brain       | 🧠 |
| `/target` | target      | 🎯 |
| `/write`  | pencil      | ✍️ |
| `/file`   | folder      | 📂 |
| `/heart`  | heart       | ❤️ |

**Sous-types ajoutés** (nouveaux, SVG dès le départ)
| Sous-type | Icône Lucide |
|-----------|-------------|
| `/code`   | code `< >` |
| `/lock`   | lock        |
| `/link`   | link        |

---

## 3. Suppression du sous-type `/bug` (doublon natif Obsidian)

**Problème**
`/bug` (🐞) dupliquait le callout natif `[!bug]` d'Obsidian, qui dispose déjà de son propre icône Lucide et est recoloré par le thème.

**Fix appliqué**
Suppression des deux occurrences dans `theme.css` :
- La définition de variable `--hud-icon: "🐞"`
- Le sélecteur `::before` correspondant

Utiliser `[!bug]` directement (couleur rouge, déjà stylisé par le thème).

---

## 4. Couleur du titre des callouts custom alignée sur Obsidian natif

**Problème**
Les callouts custom (`nav`, `status`, `projects`) n'avaient pas `--callout-color` défini. Obsidian utilise cette variable pour colorer le texte du titre (`.callout-title-inner`). Le titre n'adoptait donc pas la couleur de l'accent du callout.

**Fix appliqué**
```css
body .callout[data-callout^="nav"] {
    --callout-color: var(--color-nav);
}
body .callout[data-callout^="status"] {
    --callout-color: var(--color-status);
}
body .callout[data-callout^="projects"] {
    --callout-color: var(--color-projects);
}
```
`--callout-color` est maintenant synchronisé avec `--hud-color` pour chaque type, ce qui permet à la logique native d'Obsidian de s'appliquer (couleur du titre, fond de la barre de titre).

---

## Récapitulatif des sélecteurs modifiés

| Sélecteur | Type de modification |
|-----------|---------------------|
| `body .callout` | Ajout `isolation: isolate` |
| `body .callout[data-callout^="nav/status/projects"]` | Ajout `--callout-color` |
| `body .callout[data-callout$="/idea/brain/target/write/file/heart"]` | Remplacement emoji → SVG data URI |
| `body .callout[data-callout$="/code/lock/link"]` | Nouveaux sous-types SVG |
| `body .callout[data-callout$="/bug"]` | Supprimé (doublon natif) |
| Bloc `::before` emoji override | Supprimé (devenu inutile) |
