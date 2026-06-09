# GLOSSARY-SCHEMA.MD - Glossary Content Structure
# ComposerAtlas

**Version:** 1.0
**Status:** Active
**Last Updated:** 2026-06-08

---

## Overview

This document defines the content structure for glossary entries. Each glossary concept is a Markdown file in `src/content/glossary/`. Astro Content Collections validates every file's frontmatter against a Zod schema at build time — a missing or wrong-type field will fail the build.

---

## File Location

```
src/content/glossary/[slug].md
```

The filename must match the `slug` field in the frontmatter and must match the tag values used in `data/strategies.json`. See METRICS.MD for the canonical tag vocabulary.

---

## Frontmatter Schema

All fields below are required unless marked optional.

```yaml
---
name: string          # Display name — e.g., "RSI" or "VIX Tiers"
slug: string          # URL slug — must match filename (e.g., "rsi", "vix-tiers")
category: string      # One of: "indicator" | "risk-metric" | "asset-class" | "strategy-concept"
description: string   # One-line definition used on the glossary index card and in meta tags
formula: string       # (optional) Mathematical formula or expression — plain text or LaTeX-ish
related_tags: array   # Tags in strategies.json that map to this concept (e.g., ["rsi"])
last_updated: string  # ISO date of last content revision (e.g., "2026-06-08")
---
```

### Category Values

| Value | Display Label | Color | Used For |
|---|---|---|---|
| `indicator` | Indicator | Blue | RSI, 200d MA, momentum signals |
| `risk-metric` | Risk Metric | Pink | Sharpe ratio, Calmar ratio, max drawdown, std deviation |
| `asset-class` | Asset Class | Yellow | Leveraged ETFs, bonds, equity indices |
| `strategy-concept` | Strategy Concept | Purple | VIX tiers, defensive rotation, rebalancing |

---

## Body Sections

Every glossary entry must contain all required sections in the order below. Optional sections are marked. Section headings are H2 (`##`) to maintain proper document hierarchy under the H1 page title.

---

### Required Sections

#### `## Definition`

What this concept is. 2-4 sentences. Written for a self-directed retail investor — no assumed academic background, but no condescension either. Avoid circular definitions.

**Checklist:**
- [ ] States what the concept measures or represents
- [ ] Does not use the concept's own name to define itself
- [ ] Ends with a clear statement of what it tells an investor

---

#### `## How It Works`

The mechanics. How is this value calculated or determined? Walk through the logic step by step. Include the formula if applicable. Use plain English first; technical notation is secondary.

**Checklist:**
- [ ] Explains inputs → process → output
- [ ] If the concept has a formula, it appears here (can repeat `formula` from frontmatter with context)
- [ ] Includes a concrete, simple numeric example where possible
- [ ] Does not assume knowledge of calculus or advanced statistics

---

#### `## In Practice`

How this concept appears in Composer.trade symphonies and systematic investing strategies. What does a high/low/crossing value signal? What are common threshold values used by strategy authors?

**Checklist:**
- [ ] At least one concrete example of a strategy rule using this concept (e.g., "If RSI(14) < 30, buy QQQ")
- [ ] Explains what values are considered meaningful (e.g., RSI above 70 = overbought)
- [ ] Links to at least one relevant strategy page using `[Strategy Name](/strategies/[slug])`

---

#### `## Limitations`

What this concept does not capture. Where does it break down? What should investors be cautious about?

**Checklist:**
- [ ] At least 2 limitations listed
- [ ] Does not discourage use of the concept — just contextualizes it
- [ ] Stays practical (not academic edge cases)

---

### Optional Sections

#### `## Formula`

If the concept has a standard mathematical formula, include it here with a plain-English breakdown of each variable. Use fenced code blocks for formula notation:

```
Sharpe Ratio = (Rp − Rf) / σp

Where:
  Rp = Portfolio return
  Rf = Risk-free rate
  σp = Portfolio standard deviation
```

Omit this section if there is no standard formula (e.g., for asset-class concepts like "Leveraged ETFs").

---

#### `## Common Thresholds`

For indicators with widely-used threshold values (RSI, VIX), add a reference table:

| Value | Interpretation |
|---|---|
| RSI < 30 | Oversold — potential buy signal |
| RSI > 70 | Overbought — potential sell signal |

---

#### `## Related Concepts`

Cross-link to other glossary entries that are closely related. Use relative links:

```markdown
See also: [Calmar Ratio](/glossary/calmar-ratio), [Standard Deviation](/glossary/standard-deviation)
```

---

## Full Example: RSI

```markdown
---
name: "RSI"
slug: "rsi"
category: "indicator"
description: "A momentum oscillator that measures the speed and magnitude of recent price changes, ranging from 0 to 100."
formula: "RSI = 100 − (100 / (1 + RS)) where RS = average gain / average loss over N periods"
related_tags: ["rsi"]
last_updated: "2026-06-08"
---

## Definition

RSI (Relative Strength Index) is a momentum oscillator that measures how fast and how much an asset's price has moved over a recent period, expressed as a number between 0 and 100. It was developed by J. Welles Wilder and introduced in 1978. A high RSI suggests the asset has risen quickly and may be overextended; a low RSI suggests it has fallen quickly and may be oversold.

## How It Works

RSI is calculated over a lookback window — the standard is 14 periods (days, weeks, or bars depending on the chart timeframe).

1. Calculate the average gain across all up-periods in the window
2. Calculate the average loss (as a positive number) across all down-periods
3. Divide average gain by average loss to get RS (Relative Strength)
4. Apply the formula: `RSI = 100 − (100 / (1 + RS))`

**Example:** If over 14 days, average daily gain = 1.2% and average daily loss = 0.6%, then RS = 2.0 and RSI = 100 − (100 / 3) = **66.7**.

## In Practice

RSI is one of the most commonly used signals in Composer.trade symphonies. Strategy authors use it to filter entries and exits:

- **If RSI(14) < 30, buy** — interpreted as the asset being oversold; a potential recovery opportunity
- **If RSI(14) > 70, sell or reduce** — interpreted as overbought; reduce risk exposure

Some strategies use RSI as a confirmation signal layered on top of trend indicators like the 200-day moving average.

See the [VIX Tier Rotator](/strategies/vix-tier-rotator) for an example of RSI used alongside volatility signals.

## Formula

```
RSI = 100 − (100 / (1 + RS))

RS  = Average Gain over N periods / Average Loss over N periods
N   = Lookback period (standard: 14)
```

## Common Thresholds

| Value | Interpretation |
|---|---|
| RSI < 30 | Oversold — historically associated with potential recovery |
| RSI 30–70 | Neutral range |
| RSI > 70 | Overbought — historically associated with potential pullback |

Note: These thresholds are guidelines, not rules. Many systematic strategies use custom thresholds tuned to their specific asset and timeframe.

## Limitations

- **RSI can stay extreme:** In strong trends, RSI can remain above 70 or below 30 for extended periods. Relying solely on RSI crossings can trigger premature entries and exits.
- **Lookback sensitivity:** A 14-period RSI on a daily chart behaves very differently from a 14-period RSI on a weekly chart. The signal only means something relative to its timeframe.
- **Does not predict magnitude:** RSI signals direction tendency, not how much a price will move or for how long.

## Related Concepts

See also: [Momentum](/glossary/momentum), [200-Day Moving Average](/glossary/200d-ma)
```

---

## Glossary Entry Checklist

Before committing a new glossary entry:

- [ ] Filename matches `slug` in frontmatter
- [ ] `slug` matches a tag value used in `strategies.json` (or is documented as a standalone concept)
- [ ] `category` is one of the four valid values
- [ ] `description` is one sentence, under 160 characters (used in meta tags)
- [ ] All required sections are present: Definition, How It Works, In Practice, Limitations
- [ ] At least one cross-link to a real strategy page
- [ ] No HTML tags in body content — Markdown only
- [ ] `last_updated` is today's date
- [ ] Build passes locally: `npm run build`

---

## Canonical Glossary Entries (MVP)

| Slug | Name | Category | Status |
|---|---|---|---|
| `rsi` | RSI | indicator | Planned |
| `200d-ma` | 200-Day Moving Average | indicator | Planned |
| `momentum` | Momentum Investing | strategy-concept | Planned |
| `vix-tiers` | VIX Tiers | strategy-concept | Planned |
| `leveraged-etfs` | Leveraged ETFs | asset-class | Planned |
| `sharpe-ratio` | Sharpe Ratio | risk-metric | Planned |
| `calmar-ratio` | Calmar Ratio | risk-metric | Planned |
| `max-drawdown` | Max Drawdown | risk-metric | Planned |

Update status to "Complete" as each entry is written and committed.
