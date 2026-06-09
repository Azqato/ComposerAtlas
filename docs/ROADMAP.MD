# ROADMAP.MD - Development Roadmap
# ComposerAtlas

**Version:** 1.0
**Status:** Active
**Last Updated:** 2026-06-08

---

## V1.0 - MVP (Current)

**Target:** Launch Day (2026-06-08)
**Status:** In Development

### Deliverables
- [ ] Framework selected and scaffolded by Claude Code
- [ ] GitHub Pages deployment configured
- [ ] `/data/strategies.json` initialized with 10 strategies
- [ ] Strategy index page (lists all 10 with key metrics)
- [ ] 10 individual strategy pages (logic breakdown, metrics table, Composer CTA)
- [ ] Concept glossary index page
- [ ] Glossary pages: RSI, 200d MA, Momentum, VIX Tiers, Leveraged ETFs, Sharpe Ratio, Calmar Ratio, Max Drawdown
- [ ] Cross-linking: strategy pages link to glossary; glossary pages link to strategies
- [ ] Top navigation: Strategies, Glossary, About, Support
- [ ] About page
- [ ] Support button linking to https://azqato.github.io/support.html
- [ ] Dark mode design system applied (DESIGN.MD)
- [ ] Mobile responsive layout
- [ ] PATCHNOTES.MD entry for V1.0

---

## V1.1 - Polish + AdSense

**Target:** Post-launch (within 2 weeks)
**Status:** Planned

### Deliverables
- [ ] Google AdSense integration after approval
- [ ] Performance audit and optimization (load time, Lighthouse score)
- [ ] SEO meta tags, Open Graph, and page titles
- [ ] Favicon and social preview image
- [ ] Fix any V1.0 bugs or UI issues

---

## V1.2 - Data Tooling

**Target:** 30 days post-launch
**Status:** Planned

### Deliverables
- [ ] CLI script to update strategy metrics in `/data/strategies.json`
- [ ] Script pulls from Composer API or accepts manual CSV/JSON input
- [ ] Validation: script checks required fields before writing
- [ ] Documentation in RUNBOOK.MD updated

---

## V2.0 - Scale + Discovery

**Target:** 60-90 days post-launch
**Status:** Backlog

### Deliverables
- [ ] Client-side search across strategies and glossary (Fuse.js or similar)
- [ ] Tag-based filtering on strategy index (filter by signal type, asset class, risk level)
- [ ] Strategy comparison view (side-by-side metrics)
- [ ] Performance chart per strategy (lightweight charting library)
- [ ] Expand strategy library toward 50+ entries
- [ ] Expand glossary with additional concepts

---

## V2.1 - Community Signals

**Target:** 120+ days post-launch
**Status:** Backlog

### Deliverables
- [ ] Strategy submission form (community can suggest symphonies for review)
- [ ] Curator notes field visible on strategy pages
- [ ] Related strategies section on each strategy page

---

## V3.0 - Monetization Expansion

**Target:** TBD
**Status:** Ideation

### Deliverables
- [ ] Premium strategy tier (gated content behind one-time or subscription payment)
- [ ] Newsletter integration
- [ ] Strategy performance alerts (email or webhook)

---

## Icebox (No Timeline)

- User accounts / saved strategies
- Portfolio simulator using ComposerAtlas strategies
- Community forum or discussion layer
- Mobile app
