# TRD.MD - Technical Requirements Document
# ComposerAtlas

**Version:** 1.3
**Status:** Active
**Last Updated:** 2026-06-08

---

## Architecture Overview

ComposerAtlas is a fully static, browser-only application. There is no server, no API backend, no database service, and no authentication layer. All data is stored in flat JSON files and served via GitHub Pages. All page rendering is done in the browser via vanilla JavaScript — no build step is required.

---

## Framework Decision

**Selected: Vanilla HTML + CSS + JavaScript (Zero build tooling)**

The MVP uses no build system. All files are authored directly:

| Layer | Choice | Rationale |
|---|---|---|
| HTML | Vanilla `.html` files | No build step, open in browser or serve with any static host |
| CSS | CSS custom properties | Design token system without requiring a preprocessor |
| JavaScript | Vanilla ES2020 | `fetch()` for data, DOM manipulation for rendering |
| Fonts | Google Fonts CDN | No local build needed |
| Hosting | GitHub Pages | Serves static files directly from repository root |
| CI/CD | GitHub Actions | Upload the repo root as a Pages artifact |

This approach was chosen to eliminate the requirement for Node.js installation. The hosted site and all development workflows require zero npm/Node.js dependencies.

**Future consideration (V2):** If the site grows beyond ~20 pages or content management complexity increases, Astro 5.x remains the preferred upgrade path. The data schema, design system, and directory conventions are compatible with an Astro migration.

---

## Styling

**Selected: CSS Custom Properties (Design Tokens)**

All design tokens are defined as CSS variables in `css/main.css`. No utility framework is used in the MVP. This gives full control over every rule without a build step.

```css
:root {
  --color-bg:           #0d0d0d;
  --color-surface:      #141414;
  --color-surface-raised: #1a1a1a;
  --color-border:       #1f1f1f;
  --color-border-hover: #2e2e2e;
  --color-primary:      #f0f0f0;
  --color-secondary:    #888888;
  --color-disabled:     #444444;
  --color-green:        #00e676;
  --color-green-muted:  #00a854;
  --color-pink:         #ff4d8d;
  --color-blue:         #4d9fff;
  --color-yellow:       #f5c518;
  --color-purple:       #a78bfa;
}
```

Dark mode is the only supported mode in MVP. No light mode toggle.

---

## Directory Structure

```
composeratlas/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions → GitHub Pages (no build step)
├── css/
│   └── main.css                # Full design system: tokens, layout, components
├── data/
│   ├── strategies.json         # All 12 strategy entries (metrics + content) — source of truth
│   ├── strategies.js           # Same data as window.STRATEGIES_DATA = [...] — for file:// compat
│   ├── glossary.json           # All 8 glossary concept entries — source of truth
│   └── glossary.js             # Same data as window.GLOSSARY_DATA = [...] — for file:// compat
├── js/
│   └── app.js                  # Shared utilities: format, nav, footer, render helpers
├── strategies/
│   ├── index.html              # Strategy listing page
│   └── detail/
│       └── index.html          # Strategy detail page (reads ?slug= from URL)
├── glossary/
│   ├── index.html              # Glossary listing page
│   └── detail/
│       └── index.html          # Concept detail page (reads ?slug= from URL)
├── index.html                  # Home page (hero + strategy grid)
├── about.html                  # About page
├── 404.html                    # Custom 404 page
├── favicon.svg                 # CA monogram SVG
├── robots.txt
├── .gitignore
└── docs/                       # All documentation
```

---

## Data Layer

### strategies.json

- **Location:** `/data/strategies.json`
- **Format:** JSON array of strategy objects
- **Access:** `fetch('/data/strategies.json')` or relative equivalent at runtime
- **Schema:** See `METRICS.MD` for full field definitions
- **Updates:** Manual edits committed to GitHub; CI/CD deploys automatically

### glossary.json

- **Location:** `/data/glossary.json`
- **Format:** JSON array of concept objects
- **Schema:** Each entry has `slug`, `name`, `category`, `description`, `formula` (nullable), `related_tags`, `last_updated`, `sections` (array of `{title, paragraphs[], table?}`)

### Runtime Data Loading

Data is loaded using a dual-mode approach that works on both `file://` and HTTP origins:

**Primary — window globals (works on `file://`):**

Each HTML page includes script tags before `app.js`:
```html
<script src="data/strategies.js"></script>
<script src="data/glossary.js"></script>
<script src="js/app.js"></script>
```

`data/strategies.js` and `data/glossary.js` assign the data directly to window globals:
```javascript
window.STRATEGIES_DATA = [ ... ];
window.GLOSSARY_DATA = [ ... ];
```

**Fallback — fetch() (HTTP server or CDN):**

If the globals are absent, `app.js` falls back to `fetch()`:
```javascript
async function loadStrategies() {
  if (window.STRATEGIES_DATA) return window.STRATEGIES_DATA;
  const res = await fetch(`${BASE}/data/strategies.json`);
  return res.json();
}
```

**Source of truth:** `data/strategies.json` and `data/glossary.json`. When updating data, keep the corresponding `.js` files in sync.

**Result:** The site works by double-clicking any HTML file, via Python HTTP server, or on GitHub Pages — with identical behavior in all environments.

---

## Base URL Handling

```javascript
// js/app.js — runs on every page
const BASE = window.location.pathname.startsWith('/composeratlas')
  ? '/composeratlas'
  : '';
```

This detects whether the site is running on GitHub Pages (where the base path is `/composeratlas`) or locally (where the base path is empty). All navigation links and fetch calls use `BASE` as a prefix.

**Local development:** Open any `.html` file directly in a browser (double-click or drag to browser), or serve with any static file server:
```bash
python -m http.server 8000
# → Site at http://localhost:8000/
```

---

## Page Routes

| URL (GitHub Pages) | File | Rendering |
|---|---|---|
| `/composeratlas/` | `index.html` | JS fetches strategies.json, renders grid |
| `/composeratlas/strategies/` | `strategies/index.html` | JS renders full strategy grid |
| `/composeratlas/strategies/detail/?slug=foo` | `strategies/detail/index.html` | JS reads slug, renders detail |
| `/composeratlas/glossary/` | `glossary/index.html` | JS renders concept grid |
| `/composeratlas/glossary/detail/?slug=foo` | `glossary/detail/index.html` | JS reads slug, renders detail |
| `/composeratlas/about.html` | `about.html` | Static HTML |
| `/composeratlas/404.html` | `404.html` | GitHub Pages error page |

---

## Build and Deploy Pipeline

### Local Development (No Node.js Required)

```bash
# Option 1: Python built-in HTTP server (usually pre-installed)
python -m http.server 8000
# → http://localhost:8000/

# Option 2: Any static file server
# VS Code Live Server extension also works
```

### GitHub Actions Workflow

The workflow uploads the repository root directly as a GitHub Pages artifact — no build step needed.

```yaml
# .github/workflows/deploy.yml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: '.'
      - uses: actions/deploy-pages@v4
        id: deployment
```

---

## SEO Strategy

### Per-Page Meta

Every page sets its own `<title>` and `<meta name="description">`. Detail pages update these dynamically via JavaScript once the data is loaded.

### Open Graph

Static pages have static OG tags. Detail pages update OG tags via JS.

### Sitemap

A `sitemap.xml` can be added manually or generated via a simple script in a future iteration.

### robots.txt

```
User-agent: *
Allow: /

Sitemap: https://azqato.github.io/composeratlas/sitemap.xml
```

---

## Performance Requirements

| Metric | Target |
|---|---|
| Lighthouse Performance | 85+ |
| Lighthouse Accessibility | 90+ |
| Largest Contentful Paint (LCP) | < 2s (desktop, fast connection) |
| Total page weight (home) | < 500KB uncompressed |
| JS shipped to client | < 15KB (app.js + page scripts) |

Google Fonts are loaded from CDN. The CSS and JS files are small enough to not require bundling or minification for MVP targets.

---

## Browser Support

- Chrome 100+, Firefox 100+, Safari 15+, Edge 100+
- No IE11 support
- Requires: `fetch()`, `URLSearchParams`, `CSS custom properties`, `async/await`

---

## Security

- Strategy data is read-only JSON — no user input is accepted
- All external links use `target="_blank" rel="noopener noreferrer"`
- No API keys or secrets exist in client code or repository
- No `innerHTML` with unsanitized values — all strategy and glossary content is authored and committed directly to JSON

---

## Future Technical Considerations (V2+)

| Feature | Approach |
|---|---|
| Client-side search | Fuse.js filter over loaded JSON |
| Tag filtering on index | Vanilla JS DOM filter |
| Performance charts | Chart.js or uPlot — loaded as a module script |
| Metric update script | Python CLI: reads Composer export, validates schema, writes JSON |
| Build system upgrade | Migrate to Astro 5.x for static pre-rendering, better SEO |
| Analytics | Plausible (privacy-friendly) — post-MVP only |
