# RUNBOOK.MD - Operational Runbook
# ComposerAtlas

**Version:** 1.6
**Last Updated:** 2026-06-08

---

## Overview

This runbook covers all day-to-day operational tasks for maintaining ComposerAtlas: setting up a local dev environment, adding strategies, updating metrics, adding glossary entries, deploying changes, and handling common issues.

---

## Local Development Setup

### Prerequisites

- A static file server (no Node.js required — Python is pre-installed on most systems)
- Git

### Serve Locally

The site works without any server — just open an HTML file:

```
Option 1: Double-click index.html (or any .html file) in your file explorer
Option 2: Drag index.html into a browser window
```

Or use a local HTTP server for a more production-like experience:

```bash
# Python (usually pre-installed)
python -m http.server 8000
# → Site at http://localhost:8000/

# VS Code Live Server extension
# Right-click index.html → Open with Live Server
```

### Validate JSON

Before committing changes to `strategies.json` or `glossary.json`:

```bash
# Python (any version)
python -c "import json; json.load(open('data/strategies.json')); print('Valid JSON')"

# Or paste the file contents into jsonlint.com
```

A malformed JSON file will cause fetch errors in the browser on every page that loads the data.

---

## Adding a New Strategy

### Step 1: Gather Metrics

Collect all required fields from Composer.trade for the strategy. See METRICS.MD for the full field list. Required fields:

- `slug`, `name`, `symphony_url`, `symphony_id`
- `annualized_rate_of_return`, `max_drawdown`, `cumulative_return`
- `calmar_ratio`, `sharpe_ratio`, `standard_deviation`
- `min`, `mean`, `median`, `max`
- `trailing_one_month_return`, `trailing_three_month_return`, `trailing_one_year_return`
- `backtest_days`, `description`, `tags`, `last_updated`

### Step 2: Generate a Slug

Derive the slug from the strategy name (lowercase, hyphen-separated):
- "VIX Tier Rotator" → `vix-tier-rotator`
- See METRICS.MD for slug convention rules

### Step 3: Update strategies.json and strategies.js

Open `data/strategies.json` and append a new entry to the array. Then make the identical change in `data/strategies.js` (inside the `window.STRATEGIES_DATA = [...]` array). Both files must be kept in sync.

```json
{
  "slug": "strategy-slug-here",
  "name": "Strategy Name Here",
  "symphony_url": "https://app.composer.trade/symphony/SYMPHONY_ID/details",
  "symphony_id": "SYMPHONY_ID",
  "annualized_rate_of_return": 0.000,
  "max_drawdown": -0.000,
  "cumulative_return": 0.000,
  "calmar_ratio": 0.00,
  "sharpe_ratio": 0.00,
  "standard_deviation": 0.000,
  "min": -0.000,
  "mean": 0.000,
  "median": 0.000,
  "max": 0.000,
  "trailing_one_month_return": 0.000,
  "trailing_three_month_return": 0.000,
  "trailing_one_year_return": 0.000,
  "backtest_days": 0,
  "description": "Plain-English description of the strategy. 1-3 sentences. No HTML.",
  "tags": ["tag1", "tag2"],
  "last_updated": "YYYY-MM-DD",
  "how_it_works": [
    "Paragraph 1 explaining how the strategy works.",
    "Paragraph 2 with additional detail."
  ],
  "signals": [
    { "name": "Signal Name", "tag": "related-tag", "description": "What this signal does." }
  ],
  "risk_profile": "Risk level and description for investors considering this strategy."
}
```

### Step 4: Verify Tags

Confirm every tag in the strategy's `tags` array has a matching glossary entry in `data/glossary.json`. See METRICS.MD for the canonical tag vocabulary.

### Step 5: Test Locally

```bash
python -m http.server 8000
# Navigate to http://localhost:8000/strategies/detail/?slug=strategy-slug-here
# Verify the strategy page renders correctly with all metrics and content
```

### Step 6: Update PATCHNOTES.MD

Add a brief entry under the current version:
```
### Added
- [Strategy Name] strategy page (slug: strategy-slug)
```

### Step 7: Commit and Push

```bash
git add data/strategies.json data/strategies.js docs/PATCHNOTES.MD
git commit -m "feat: add [Strategy Name] strategy"
git push origin main
```

GitHub Actions will deploy automatically within 1-2 minutes.

---

## Regular Data Updates (Required)

ComposerAtlas must be kept in sync with live Composer.trade data. Two types of data require regular refresh:

| Data | File(s) | Frequency | Why |
|---|---|---|---|
| Backtest metrics (ARR, Sharpe, DD, trailing returns…) | `data/strategies.json` + `data/strategies.js` | Monthly | Markets move; metrics drift from current reality |
| Symphony logic trees (full IF/ELSE structure) | `data/symphony_scores.json` | When symphonies are edited | Strategy authors update logic; site analysis must stay accurate |

Both are updated automatically by running one script:

```bash
python scripts/update_metrics.py
```

This script:
1. Hits `POST /api/v0.1/symphonies/{id}/backtest` for all 12 strategies → rewrites `data/strategies.json` and `data/strategies.js`
2. Hits `GET /api/v0.1/symphonies/{id}/score` for all 12 strategies → rewrites `data/symphony_scores.json`

No API key required. Run from the project root.

**symphony_scores.json is for AI analysis only** — it is not served to the website. It contains the full EDN logic tree for each symphony, used to understand and explain strategy logic in future conversations.

After running the script:

```bash
git add data/strategies.json data/strategies.js data/symphony_scores.json
git commit -m "data: refresh metrics and symphony scores - YYYY-MM-DD"
git push origin main
```

---

## Updating Strategy Metrics (Manual)

If you need to update a single strategy without running the full script:

### Step 1: Open strategies.json and strategies.js

Locate the strategy entry by `slug` or `symphony_id` in both files. Both must be updated.

### Step 2: Update Fields

Update any changed metric values and set `last_updated` to today's date (`YYYY-MM-DD`).

**Important:** `max_drawdown` is always stored as a negative number (e.g., `-0.432`, not `0.432`).

### Step 3: Validate JSON

```bash
python -c "import json; json.load(open('data/strategies.json')); print('Valid JSON')"
```

### Step 4: Commit and Push

```bash
git add data/strategies.json data/strategies.js
git commit -m "data: update metrics for [Strategy Name] - YYYY-MM-DD"
git push origin main
```

---

## Adding a Glossary Entry

### Step 1: Add to glossary.json and glossary.js

Open `data/glossary.json` and append a new entry. Then make the identical addition in `data/glossary.js` (inside the `window.GLOSSARY_DATA = [...]` array). Both files must stay in sync.

```json
{
  "slug": "concept-slug",
  "name": "Concept Name",
  "category": "indicator",
  "description": "One-sentence description used on the card and in meta tags.",
  "formula": "Optional formula string, or null",
  "related_tags": ["concept-slug"],
  "last_updated": "YYYY-MM-DD",
  "sections": [
    {
      "title": "Definition",
      "paragraphs": ["Paragraph 1.", "Paragraph 2."]
    },
    {
      "title": "How It Works",
      "paragraphs": ["Explanation paragraph."],
      "table": {
        "headers": ["Column 1", "Column 2"],
        "rows": [
          ["Row 1 Col 1", "Row 1 Col 2"]
        ]
      }
    },
    {
      "title": "In Practice",
      "paragraphs": ["How this applies to Composer.trade strategies."]
    },
    {
      "title": "Limitations",
      "paragraphs": ["Known limitations and caveats."]
    }
  ]
}
```

Valid `category` values: `"indicator"`, `"risk-metric"`, `"asset-class"`, `"strategy-concept"`

### Step 2: Tag Related Strategies

Ensure the relevant strategies in `data/strategies.json` include this concept's tag in their `tags` array.

### Step 3: Test Locally

```bash
python -m http.server 8000
# Navigate to http://localhost:8000/glossary/detail/?slug=concept-slug
# Verify the page renders and the "Related Strategies" sidebar populates
```

### Step 4: Commit and Push

```bash
git add data/glossary.json data/glossary.js data/strategies.json docs/PATCHNOTES.MD
git commit -m "feat: add glossary entry for [Concept Name]"
git push origin main
```

---

## Removing a Strategy

If a strategy becomes unavailable on Composer.trade:

### Step 1: Remove from strategies.json

Delete the entry from the array.

### Step 2: Update PATCHNOTES.MD

Document the removal with a reason:
```
### Removed
- [Strategy Name] — [reason: e.g., symphony deleted from Composer.trade]
```

### Step 3: Commit and Push

```bash
git add data/strategies.json docs/PATCHNOTES.MD
git commit -m "remove: [Strategy Name] - [reason]"
git push origin main
```

---

## Deployment

ComposerAtlas deploys automatically via GitHub Actions on every push to `main`. No manual deployment steps are required.

**Deployment URL:** `https://azqato.github.io/composeratlas`
**Repository:** `https://github.com/Azqato/ComposerAtlas`
**Deploy time:** Typically 1-2 minutes after push

The workflow uses `rsync` to build a clean `_site/` folder before uploading to GitHub Pages. The following are intentionally excluded from the public deployment:

- `data/symphony_scores.json` — AI analysis file; not user-facing (14MB)
- `docs/` — Internal project documentation
- `scripts/` — Python data sync scripts
- `strategies.xlsx` — Source spreadsheet
- `README.MD`, `.gitignore`, `.github/`

To monitor deployment status:
1. Go to `https://github.com/Azqato/ComposerAtlas` → **Actions** tab
2. Find the most recent "Deploy to GitHub Pages" run
3. Green checkmark = deployed; red X = failed (check logs)

---

## Checking for Broken Composer Links

Periodically verify that `symphony_url` links still resolve on Composer.trade:

1. Open `data/strategies.json`
2. For each entry, open the `symphony_url` in a browser
3. If a link is broken, either update the URL or remove the strategy
4. Recommended frequency: monthly

---

## Common Issues

### Site Not Updating After Push

1. Go to GitHub → Actions tab → check build/deploy status
2. If deployment failed: check the error log
3. If deployed but site looks old: hard refresh (Ctrl+Shift+R / Cmd+Shift+R) or clear browser cache
4. GitHub Pages CDN propagation can take up to 5 minutes

### Strategy or Glossary Page Shows Blank / Spinner Stuck

- Open browser developer tools → Console tab
- Check for JavaScript errors
- Confirm `data/strategies.js` and `data/glossary.js` exist and contain valid JavaScript (the `window.STRATEGIES_DATA = [...]` assignment must be syntactically correct)
- If using a Python HTTP server, also check that `data/strategies.json` and `data/glossary.json` are valid JSON

### JSON Parse Error in Browser

- Open browser console — look for `SyntaxError: Unexpected token`
- Validate the file: `python -c "import json; json.load(open('data/strategies.json')); print('OK')"`
- Common causes: trailing comma on last array item, missing quotes, unclosed bracket
- Fix and push again

### Strategy Not Found Page

- Confirm the `slug` in `strategies.json` matches the `?slug=` query parameter in the URL exactly
- Check for typos (slugs are case-sensitive)

### Local Dev — Data Not Loading When Opened Directly

- If opening `.html` files directly and data is missing, confirm that `data/strategies.js` and `data/glossary.js` are present in the repo
- Confirm each HTML page has `<script src="[path]/data/strategies.js">` and `<script src="[path]/data/glossary.js">` before the `app.js` script tag
- The relative path depth must match the HTML file's location: root pages use `data/`, first-level uses `../data/`, second-level uses `../../data/`

---

## Re-Analyzing Strategy Logic Trees

When a symphony author updates their logic (or when the existing `how_it_works`, `signals`, `tags`, or `risk_profile` content needs to be verified against the actual IF/ELSE structure), follow this process.

**When to run this:**
- After `scripts/update_metrics.py` is run and `data/symphony_scores.json` is refreshed
- When a strategy's description seems to contradict what it's actually doing
- When adding new tags to the glossary and needing to verify which strategies use the concept
- When the strategy author announces a logic change on Composer.trade

---

### Step 1: Refresh symphony_scores.json

```bash
python scripts/update_metrics.py
```

This re-fetches both backtest metrics and full logic trees. `data/symphony_scores.json` is overwritten. If you only need the logic trees without changing metrics, you can isolate that function by editing the script — but the combined run is safe.

---

### Step 2: Have an AI analyze the logic trees

`data/symphony_scores.json` is ~14MB of raw EDN-format logic tree data. It is not human-readable without assistance. Use an AI (Claude Code, or any AI with a large context window) and provide the file for analysis.

**What to ask for:**
1. Parse each symphony's `score.definition` tree into human-readable IF/ELSE pseudocode
2. Identify:
   - Primary trend gate (SMA, MaxDD, cumulative return, etc.)
   - Overbought guards (RSI thresholds, specific ETFs tested)
   - Oversold dip-buy conditions (RSI levels and ETF targets)
   - Volatility routing (UVXY tier thresholds and routing outcomes)
   - Cross-asset signals (bonds, commodities, sector ETFs)
   - Terminal leaf allocations (what ETFs are actually held)
3. Compare findings to existing `how_it_works`, `signals`, and `tags` in `data/strategies.json`
4. Flag discrepancies between the existing content and the actual logic

**Structural pattern to look for (2026 library):**

All 12 symphonies in this library share a common structure:

```
[EqualWeight]
  ├── zoop's 2026 Frontrunner (50%)    ← shared base component
  └── [Strategy-specific component] (50%)
```

The Frontrunner component always contributes:
- RSI(10) oversold dip-buys: SMH <23 → SOXL; QQQ <28 → TQQQ; SPY <28 → UPRO
- XLY RSI >79 → VXX (overbought volatility hedge)
- UVXY RSI >65 → SPXU (if SPY return > MA return) or TQQQ
- Default: BIL (T-bills)

Therefore every strategy in this library automatically inherits `rsi` and `vix-tiers` tags.

---

### Step 3: Update strategies.json (and strategies.js)

For each strategy, update these fields based on the analysis:

**`tags` array** — Canonical tag vocabulary:
- `rsi` — Any RSI signal used (almost always present)
- `200d-ma` — `Price(X) > SMA()` comparison used as a primary trend gate
- `momentum` — Cumulative return comparisons, EMA vs SMA, relative RSI checks, bond momentum
- `leveraged-etfs` — Strategy holds 2x or 3x leveraged ETFs
- `vix-tiers` — UVXY/VIX RSI threshold routing (present in all 12 via Frontrunner)
- `max-drawdown` — MaxDD() used as a primary signal (only SOXL Growth in 2026 library)

**`signals` array** — Each object: `{ "name": ..., "tag": ..., "description": ... }`
- `tag` should match one of the canonical tags above
- `description` should include the specific ETickers, RSI thresholds, and routing outcome
- List the most important signals first; include the UVXY check as the vix-tiers entry

**`how_it_works` array** — Three paragraphs:
1. What the strategy does at the highest level; the primary trend gate; what it defaults to
2. The overbought/volatility guard system; how it handles overextended markets
3. The bear-regime or edge-case protocol; special features not found in other strategies

**`risk_profile` string** — One paragraph:
- Risk characterization (Aggressive / Extremely Aggressive)
- Reference actual metric values from the current `max_drawdown` and `standard_deviation` fields
- Note anything that makes this strategy's risk profile different from similar strategies

After editing `strategies.json`, make the identical change in `strategies.js` (inside the `window.STRATEGIES_DATA = [...]` array).

---

### Step 4: Validate and test

```bash
# Validate JSON is well-formed
python -c "import json; json.load(open('data/strategies.json')); print('Valid JSON')"

# Serve locally and spot-check a few strategy pages
python -m http.server 8000
# Open http://localhost:8000/strategies/detail/?slug=zoops-2026-frontrunner
```

Verify that the "How It Works", "Signals", and "Risk Profile" sections render the updated content correctly.

---

### Step 5: Update PATCHNOTES.MD

Add a minor version entry (1.X.0) documenting:
- Which strategies changed and what was corrected
- Tag changes with before/after
- Key content corrections (most impactful inaccuracies fixed)

---

### Step 6: Commit

```bash
git add data/strategies.json data/strategies.js data/symphony_scores.json docs/PATCHNOTES.MD
git commit -m "content: re-analyze logic trees and update strategy content - YYYY-MM-DD"
git push origin main
```

---

## Versioning Convention

| Change Type | Version Bump | Example |
|---|---|---|
| New site sections, breaking schema changes | Major (X.0.0) | Adding user auth |
| New features, new strategies, new glossary entries | Minor (1.X.0) | Adding a new strategy page |
| Metric updates, bug fixes, copy corrections | Patch (1.0.X) | Refreshing trailing return figures |

Update PATCHNOTES.MD for every change, including data-only updates.
