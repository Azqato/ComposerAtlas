# GLOSSARY-SCHEMA.MD - Glossary Content Structure
# ComposerAtlas

**Version:** 2.0
**Status:** Active
**Last Updated:** 2026-06-08

---

## Overview

This document defines the content structure for glossary entries. All glossary concepts are stored as objects in `data/glossary.json` (the source of truth) and duplicated in `data/glossary.js` (for `file://` protocol compatibility). There is no build step — the `glossary/detail/index.html` page reads the data at runtime and renders it dynamically.

---

## File Location

```
data/glossary.json   ← source of truth; edit this first
data/glossary.js     ← must be kept in sync (window.GLOSSARY_DATA = [...])
```

Both files contain the same data. After editing `glossary.json`, make the identical change in `glossary.js`.

---

## JSON Entry Schema

All fields below are required unless marked optional.

```json
{
  "slug": "string",          // URL slug — e.g., "rsi", "vix-tiers". Must match tag values in strategies.json.
  "name": "string",          // Display name — e.g., "RSI", "VIX Tiers"
  "category": "string",      // One of: "indicator" | "risk-metric" | "asset-class" | "strategy-concept"
  "description": "string",   // One-sentence definition; used on glossary index card and in meta tags
  "formula": "string|null",  // (optional) Mathematical formula in plain text, or null
  "related_tags": ["string"],// Tags in strategies.json that map to this concept (e.g., ["rsi"])
  "last_updated": "string",  // ISO date of last content revision (e.g., "2026-06-08")
  "sections": [              // Array of content sections rendered on the detail page
    {
      "title": "string",
      "paragraphs": ["string"],
      "table": {              // (optional) Rendered as a two-column table within the section
        "headers": ["string"],
        "rows": [["string"]]
      }
    }
  ]
}
```

### Category Values

| Value | Display Label | Badge Color | Used For |
|---|---|---|---|
| `indicator` | Indicator | Blue | RSI, 200d MA, momentum signals |
| `risk-metric` | Risk Metric | Pink | Sharpe ratio, Calmar ratio, max drawdown, std deviation |
| `asset-class` | Asset Class | Yellow | Leveraged ETFs, bonds, equity indices |
| `strategy-concept` | Strategy Concept | Purple | VIX tiers, defensive rotation, rebalancing |

---

## Sections

Each entry in the `sections` array has a `title` and `paragraphs` array. The page renders each string in `paragraphs` as a `<p>` tag. An optional `table` object renders a two-column reference table after the paragraphs.

### Required Sections (in order)

#### `Definition`

What this concept is. 2-4 sentences. Written for a self-directed retail investor — no assumed academic background, but no condescension either. Avoid circular definitions.

**Checklist:**
- [ ] States what the concept measures or represents
- [ ] Does not use the concept's own name to define itself
- [ ] Ends with a clear statement of what it tells an investor

---

#### `How It Works`

The mechanics. How is this value calculated or determined? Walk through the logic step by step. Include the formula if applicable. Use plain English first; technical notation is secondary.

**Checklist:**
- [ ] Explains inputs → process → output
- [ ] If the concept has a formula, it appears here with context
- [ ] Includes a concrete, simple numeric example where possible
- [ ] Does not assume knowledge of calculus or advanced statistics

---

#### `In Practice`

How this concept appears in Composer.trade symphonies and systematic investing strategies. What does a high/low/crossing value signal? What are common threshold values used by strategy authors?

**Checklist:**
- [ ] At least one concrete example of a strategy rule using this concept (e.g., "If RSI(14) < 30, buy QQQ")
- [ ] Explains what values are considered meaningful (e.g., RSI above 70 = overbought)

---

#### `Limitations`

What this concept does not capture. Where does it break down? What should investors be cautious about?

**Checklist:**
- [ ] At least 2 limitations listed
- [ ] Does not discourage use of the concept — just contextualizes it
- [ ] Stays practical (not academic edge cases)

---

### Optional Sections

Additional sections can be added for any concept that warrants them. Common examples:

- **`Building with [Concept] in Composer.trade`** — essay on applying the concept when building symphonies; all 8 MVP entries include this
- **`Common Thresholds`** — use a `table` to list canonical values (RSI < 30 = oversold, etc.)
- **`Formula`** — detailed formula breakdown when the concept has standard notation

---

## Full Example: RSI Entry

```json
{
  "slug": "rsi",
  "name": "RSI",
  "category": "indicator",
  "description": "A momentum oscillator that measures the speed and magnitude of recent price changes, ranging from 0 to 100.",
  "formula": "RSI = 100 - (100 / (1 + RS)) where RS = average gain / average loss over N periods",
  "related_tags": ["rsi"],
  "last_updated": "2026-06-08",
  "sections": [
    {
      "title": "Definition",
      "paragraphs": [
        "RSI (Relative Strength Index) is a momentum oscillator that measures how fast and how much an asset's price has moved over a recent period, expressed as a number between 0 and 100.",
        "A high RSI suggests the asset has risen quickly and may be overextended; a low RSI suggests it has fallen quickly and may be oversold."
      ]
    },
    {
      "title": "How It Works",
      "paragraphs": [
        "RSI is calculated over a lookback window — the standard is 14 periods. Calculate the average gain and average loss across all periods in the window. Divide average gain by average loss to get RS (Relative Strength), then apply: RSI = 100 − (100 / (1 + RS)).",
        "Example: If over 14 days, average daily gain = 1.2% and average loss = 0.6%, then RS = 2.0 and RSI = 66.7."
      ]
    },
    {
      "title": "In Practice",
      "paragraphs": [
        "RSI is one of the most commonly used signals in Composer.trade symphonies. The zoop 2026 library uses RSI(10) for oversold dip-buys: SMH RSI < 23 → SOXL; QQQ RSI < 28 → TQQQ; SPY RSI < 28 → UPRO.",
        "Overbought checks also use RSI: XLY RSI > 79 routes to VXX as a volatility hedge."
      ],
      "table": {
        "headers": ["Threshold", "Interpretation"],
        "rows": [
          ["RSI < 30", "Oversold — potential dip-buy opportunity"],
          ["RSI 30-70", "Neutral range"],
          ["RSI > 70", "Overbought — consider reducing exposure"]
        ]
      }
    },
    {
      "title": "Limitations",
      "paragraphs": [
        "RSI can stay extreme: in strong trends, RSI can remain above 70 or below 30 for extended periods. Relying solely on RSI crossings can trigger premature entries and exits.",
        "Lookback sensitivity: A 14-period RSI on a daily chart behaves very differently from a 14-period RSI on a weekly chart.",
        "Does not predict magnitude: RSI signals direction tendency, not how much a price will move or for how long."
      ]
    },
    {
      "title": "Building with RSI in Composer.trade",
      "paragraphs": [
        "RSI is best used as a secondary confirmation signal rather than a standalone entry rule. Pairing RSI with a trend filter (200d MA or cumulative return) reduces false signals in choppy markets.",
        "Custom thresholds often outperform the standard 30/70 levels for individual ETFs. The zoop library uses RSI(10) < 23 for SMH and < 28 for QQQ and SPY — tighter than the textbook thresholds because these are short-term dip-buy signals, not full reversal entries."
      ]
    }
  ]
}
```

---

## Adding a New Glossary Entry

### Step 1: Add to glossary.json

Append a new entry to the array in `data/glossary.json`.

### Step 2: Sync to glossary.js

Make the identical addition inside the `window.GLOSSARY_DATA = [...]` array in `data/glossary.js`.

### Step 3: Tag related strategies

Ensure the relevant strategies in `data/strategies.json` include this concept's slug in their `tags` array.

### Step 4: Validate JSON

```bash
python -c "import json; json.load(open('data/glossary.json')); print('Valid JSON')"
```

### Step 5: Test locally

```bash
python -m http.server 8000
# Navigate to http://localhost:8000/glossary/detail/?slug=your-slug
```

---

## Glossary Entry Checklist

Before committing a new glossary entry:

- [ ] `slug` is lowercase, hyphen-separated, unique
- [ ] `slug` matches a tag value used in `strategies.json` (or is documented as a standalone concept)
- [ ] `category` is one of the four valid values
- [ ] `description` is one sentence, under 160 characters
- [ ] `sections` array includes all required sections: Definition, How It Works, In Practice, Limitations
- [ ] `last_updated` is today's date
- [ ] Identical entry added to `data/glossary.js`
- [ ] JSON validates: `python -c "import json; json.load(open('data/glossary.json')); print('OK')"`
- [ ] Detail page renders correctly at `http://localhost:8000/glossary/detail/?slug=your-slug`

---

## Canonical Glossary Entries (MVP)

| Slug | Name | Category | Status |
|---|---|---|---|
| `rsi` | RSI | indicator | Complete |
| `200d-ma` | 200-Day Moving Average | indicator | Complete |
| `momentum` | Momentum Investing | strategy-concept | Complete |
| `vix-tiers` | VIX Tiers | strategy-concept | Complete |
| `leveraged-etfs` | Leveraged ETFs | asset-class | Complete |
| `sharpe-ratio` | Sharpe Ratio | risk-metric | Complete |
| `calmar-ratio` | Calmar Ratio | risk-metric | Complete |
| `max-drawdown` | Max Drawdown | risk-metric | Complete |

All 8 MVP entries are live and include a "Building with..." essay section (added v1.0.6).
