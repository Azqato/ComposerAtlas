# ROADMAP.MD - Development Roadmap
# ComposerAtlas

**Version:** 1.1
**Status:** Active
**Last Updated:** 2026-06-08

---

## V1.0 - MVP

**Launched:** 2026-06-08
**Status:** Complete
**Live URL:** https://azqato.github.io/ComposerAtlas/

### Deliverables
- [x] Framework selected and scaffolded by Claude Code (Vanilla HTML/CSS/JS — zero build dependencies)
- [x] GitHub Pages deployment configured (rsync workflow; internal docs/scripts excluded from public deploy)
- [x] `/data/strategies.json` initialized with 12 strategies
- [x] Strategy index page (lists all 12 with key metrics)
- [x] 12 individual strategy pages (logic breakdown, metrics table, Composer CTA)
- [x] Concept glossary index page
- [x] Glossary pages: RSI, 200d MA, Momentum, VIX Tiers, Leveraged ETFs, Sharpe Ratio, Calmar Ratio, Max Drawdown
- [x] Cross-linking: strategy pages link to glossary; glossary pages link to strategies
- [x] Top navigation: Strategies, Glossary, About, Support
- [x] About page
- [x] Support button linking to https://azqato.github.io/support.html
- [x] Dark mode design system applied (DESIGN.MD)
- [x] Mobile responsive layout
- [x] PATCHNOTES.MD entry for V1.0

---

## V1.1 - Polish + Content Quality

**Target:** Post-launch (within 2 weeks)
**Status:** In Progress (current; at v1.1.3)

### Deliverables
- [ ] Google AdSense integration after approval
- [ ] Performance audit and optimization (load time, Lighthouse score)
- [x] SEO meta tags and page titles (completed in V1.0 build)
- [x] Favicon (completed in V1.0 build)
- [x] Fix V1.0 bugs or UI issues:
  - [x] Navigation broken on `file://` protocol — `u()` helper added (v1.0.4)
  - [x] All links broken on GitHub Pages — case-insensitive BASE detection (v1.1.2)
  - [x] Footer layout simplified to match azqato.github.io (v1.1.3)
- [x] Strategy content quality: all 12 symphonies rewritten from logic tree analysis (v1.1.0)
- [x] Script-based metric updates: `scripts/update_metrics.py` live (v1.0.8)

---

## V1.2 - Data Tooling

**Target:** 30 days post-launch
**Status:** Partially Complete

### Deliverables
- [x] CLI script to update strategy metrics and logic trees (`scripts/update_metrics.py` — v1.0.8)
- [x] Script pulls from Composer API (backtest + score endpoints) — no API key required
- [ ] Validation: script checks required fields before writing
- [x] Documentation in RUNBOOK.MD updated (v1.6)

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
