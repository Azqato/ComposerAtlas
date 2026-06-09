# ComposerAtlas

> A curated strategy library and education hub that maps out Composer.trade investing strategies alongside the signals, concepts, and logic that power them.

## What Is This?

ComposerAtlas is a browser-only, static website hosted on GitHub Pages. It showcases curated Composer.trade symphonies with deep breakdowns of their logic, metrics, and underlying investing concepts. It also serves as a reference glossary for signals and indicators like RSI, 200-day moving averages, VIX tiers, momentum, and leveraged ETFs.

No server. No backend. No build step. No cost to run.

## Live Site

[https://azqato.github.io/composeratlas](https://azqato.github.io/composeratlas)

## Features

- 12 strategy pages with full logic breakdowns, signals, and risk profiles
- Live metrics rendered from a static JSON database (updated via GitHub commits)
- Concept glossary covering RSI, VIX tiers, momentum, 200d MA, leveraged ETFs, and more
- Cross-linked strategies and concepts — every strategy links to relevant glossary terms
- Direct links to open strategies on Composer.trade
- Dark mode UI inspired by Composer.trade's design language

## Tech Stack

| Layer | Technology |
|---|---|
| HTML | Vanilla `.html` files — no framework, no build step |
| CSS | CSS custom properties for design tokens, vanilla CSS for layout and components |
| JavaScript | Vanilla ES2020 — `fetch()` for data, DOM manipulation for rendering |
| Data | `/data/strategies.json` + `.js` and `/data/glossary.json` + `.js` — flat-file databases with dual-mode loading (globals for `file://`, fetch for HTTP) |
| Hosting | GitHub Pages — serves the repository root directly |
| CI/CD | GitHub Actions — deploys on every push to `main` (no build step, <2 min) |

No Node.js required. Open in any static file server or deploy directly to GitHub Pages.

## Repo Structure

```
composeratlas/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions → GitHub Pages (no build step)
├── css/
│   └── main.css                # Complete design system: tokens, layout, components
├── data/
│   ├── strategies.json         # 12 strategy entries — source of truth
│   ├── strategies.js           # Same data as window global — enables file:// loading
│   ├── glossary.json           # 8 glossary concept entries — source of truth
│   └── glossary.js             # Same data as window global — enables file:// loading
├── js/
│   └── app.js                  # Shared utilities: formats, nav, footer, render helpers
├── strategies/
│   ├── index.html              # Strategy listing page
│   └── detail/
│       └── index.html          # Strategy detail page (reads ?slug= from URL)
├── glossary/
│   ├── index.html              # Glossary listing page
│   └── detail/
│       └── index.html          # Concept detail page (reads ?slug= from URL)
├── index.html                  # Home page: hero + strategy grid
├── about.html                  # About page
├── 404.html                    # Custom 404 page
├── favicon.svg
├── robots.txt
├── .gitignore
└── docs/                       # Project documentation
    ├── PRD.MD
    ├── TRD.MD
    ├── DESIGN.MD
    ├── METRICS.MD
    ├── GLOSSARY-SCHEMA.MD
    ├── ROADMAP.MD
    ├── PATCHNOTES.MD
    ├── RUNBOOK.MD
    ├── SECURITY.MD
    ├── TENETS.MD
    └── PRFAQ.MD
```

## Local Development

No installation required. Open any HTML file directly in a browser — just double-click it:

```
index.html → home page
strategies/index.html → strategy listing
glossary/index.html → glossary
```

Or use a static file server for a more production-like experience:

```bash
# Python (usually pre-installed on macOS/Linux/Windows)
python -m http.server 8000
# → http://localhost:8000/

# Or use VS Code's Live Server extension
```

## Updating Strategies

Edit `/data/strategies.json` and commit to `main`. GitHub Pages will deploy automatically within 1-2 minutes. See [RUNBOOK.MD](docs/RUNBOOK.MD) for the full process.

## Documentation

| File | Purpose |
|---|---|
| [PRD.MD](docs/PRD.MD) | Product Requirements — goals, features, target users |
| [TRD.MD](docs/TRD.MD) | Technical Requirements — architecture, data layer, deploy pipeline |
| [DESIGN.MD](docs/DESIGN.MD) | Design System — colors, typography, components, page layouts |
| [METRICS.MD](docs/METRICS.MD) | Strategy data schema — all fields, display rules, tag vocabulary |
| [GLOSSARY-SCHEMA.MD](docs/GLOSSARY-SCHEMA.MD) | Glossary content structure — field spec, section requirements |
| [ROADMAP.MD](docs/ROADMAP.MD) | Development roadmap — MVP and post-MVP milestones |
| [RUNBOOK.MD](docs/RUNBOOK.MD) | Operational runbook — how to add strategies, deploy, debug |
| [PATCHNOTES.MD](docs/PATCHNOTES.MD) | Changelog |
| [SECURITY.MD](docs/SECURITY.MD) | Security posture and responsible disclosure |
| [TENETS.MD](docs/TENETS.MD) | Core principles guiding every decision |
| [PRFAQ.MD](docs/PRFAQ.MD) | Press release and FAQ |

## Disclaimer

ComposerAtlas is an independent educational resource. It is not affiliated with Composer Technologies, Inc. or any strategy authors. All featured strategies are presented for informational purposes only. Past performance does not guarantee future results. Nothing on this site constitutes financial advice.

## License

MIT
