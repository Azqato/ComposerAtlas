# METRICS.MD - Strategy Metrics Schema
# ComposerAtlas

**Version:** 1.2
**Status:** Active
**Last Updated:** 2026-06-08

---

## Overview

This document defines the full schema for strategy entries in `/data/strategies.json`. All fields are listed with their type, description, display guidance, and calculation notes. The schema is designed to be extensible; new optional fields can be added in future versions without breaking existing entries.

---

## MVP Schema

| Field | Type | Required | Description |
|---|---|---|---|
| `slug` | string | Yes | Human-readable URL slug (e.g., `vix-tier-rotator`). Used as the route param in `/strategies/[slug]`. Must be lowercase, hyphen-separated, unique across all entries. |
| `name` | string | Yes | Display name of the strategy (e.g., "VIX Tier Rotator") |
| `symphony_url` | string | Yes | Full URL to clone the symphony on Composer.trade |
| `symphony_id` | string | Yes | Unique Composer symphony ID as shown in the Composer URL |
| `annualized_rate_of_return` | float | Yes | Annualized return as a decimal (e.g., `0.312` = 31.2%) |
| `max_drawdown` | float | Yes | Maximum drawdown as a decimal — always negative (e.g., `-0.187` = -18.7%) |
| `cumulative_return` | float | Yes | Total cumulative return since inception as a decimal |
| `calmar_ratio` | float | Yes | Calmar ratio = annualized return / abs(max drawdown). Higher is better. |
| `sharpe_ratio` | float | Yes | Sharpe ratio = (return − risk-free rate) / standard deviation. Higher is better. |
| `standard_deviation` | float | Yes | Annualized standard deviation of returns (volatility) |
| `min` | float | Yes | Minimum single-period return observed (usually daily or monthly) |
| `mean` | float | Yes | Mean (average) single-period return |
| `median` | float | Yes | Median single-period return |
| `max` | float | Yes | Maximum single-period return observed |
| `trailing_one_month_return` | float | Yes | Return over the trailing 1-month period |
| `trailing_three_month_return` | float | Yes | Return over the trailing 3-month period |
| `trailing_one_year_return` | float | Yes | Return over the trailing 1-year period |
| `backtest_days` | integer | Yes | Length of the backtest in trading days. Display as "~X years (N trading days)" using `Math.round(backtest_days / 252)`. |
| `description` | string | Yes | Short plain-English description of the strategy (1-3 sentences). No HTML tags. |
| `tags` | array of strings | Yes | Concept tags used for glossary cross-linking. Must match `slug` values in `data/glossary.json`. |
| `last_updated` | string (ISO date) | Yes | Date metrics were last updated (`YYYY-MM-DD`) |
| `how_it_works` | array of strings | Recommended | Paragraphs explaining the strategy logic. Each string becomes a `<p>` tag on the strategy page. |
| `signals` | array of objects | Recommended | Signals used by the strategy. Each object: `{name, tag, description}`. `tag` must match a glossary slug. |
| `risk_profile` | string | Recommended | Risk summary shown in the risk profile section of the strategy page. |

---

## Metric Calculation Notes

### Calmar Ratio
`calmar_ratio = annualized_rate_of_return / abs(max_drawdown)`

Example: ARR of 31.2% and max drawdown of -18.7%:
`0.312 / 0.187 = 1.67`

A Calmar ratio above 1.0 means the strategy earns more per year than its worst drawdown.

### Sharpe Ratio
`sharpe_ratio = (annualized_return − risk_free_rate) / annualized_std_deviation`

Composer uses its own internal risk-free rate baseline. Pull the Sharpe ratio directly from Composer's displayed value for the strategy.

### Standard Deviation
Annualized standard deviation of periodic returns. Higher = more volatile.

### Period Returns (min, mean, median, max)
These values represent single-period (typically monthly) returns, not annualized. Pull directly from Composer's return distribution view.

---

## Slug Convention

- Derive from the strategy name: lowercase, spaces replaced with hyphens, remove special characters
- Examples:
  - "VIX Tier Rotator" → `vix-tier-rotator`
  - "RSI Momentum 200d" → `rsi-momentum-200d`
  - "TQQQ/BIL Switcher" → `tqqq-bil-switcher`
- Slugs must be unique. If two strategies would produce the same slug, append `-2`.
- Do not change a slug after a strategy page is live — it will break inbound links.

---

## Display Guidelines

### Formatting Rules
- All float values representing percentages: display as `+XX.X%` or `-XX.X%` (multiply by 100, always include sign for return values)
- Ratios (Sharpe, Calmar): display as plain decimal `X.XX` — no percent sign, no color coding
- All numeric values: monospace font (`font-mono`)
- `last_updated`: display as human-readable (e.g., `Jun 8, 2026`)

### Color Coding
| Value Type | Display Color |
|---|---|
| Positive return / ARR / trailing returns | `--color-green` |
| Negative return / max_drawdown | `--color-pink` |
| Calmar ratio, Sharpe ratio | `--color-primary` (no color coding) |
| Standard deviation | `--color-primary` |
| Min period return (negative) | `--color-pink` |
| Max period return (positive) | `--color-green` |
| Mean / median period return | `--color-yellow` (neutral indicator) |

### Metrics Display Order (MetricsTable)

Group 1 — Performance:
1. Ann. Return (`annualized_rate_of_return`)
2. Max Drawdown (`max_drawdown`)
3. Cumulative Return (`cumulative_return`)
4. Calmar Ratio (`calmar_ratio`)
5. Sharpe Ratio (`sharpe_ratio`)
6. Std. Deviation (`standard_deviation`)

Group 2 — Trailing Returns:
7. 1-Month Return (`trailing_one_month_return`)
8. 3-Month Return (`trailing_three_month_return`)
9. 1-Year Return (`trailing_one_year_return`)

Group 3 — Return Distribution:
10. Min Period Return (`min`)
11. Mean Period Return (`mean`)
12. Median Period Return (`median`)
13. Max Period Return (`max`)

Group 4 — Metadata:
14. Backtest Period (`backtest_days`) — displayed as "~X years (N trading days)"
15. Last Updated (`last_updated`)

---

## Extended Schema Fields (Post-MVP, Optional)

These fields are not required at MVP but are reserved for future use. Entries that include them will render additional UI elements automatically.

| Field | Type | Description |
|---|---|---|
| `inception_date` | string (ISO date) | Date the strategy was created on Composer |
| `benchmark` | string | Benchmark ticker for comparison (e.g., `"SPY"`) |
| `assets` | array of strings | List of tickers the strategy can hold |
| `rebalance_frequency` | string | How often the strategy rebalances (`"daily"`, `"weekly"`, `"monthly"`) |
| `author_note` | string | Optional curator note about the strategy (plain text, no HTML) |
| `risk_level` | string | Risk classification: `"conservative"`, `"moderate"`, `"aggressive"` |
| `trailing_five_year_return` | float | Return over trailing 5 years |
| `sortino_ratio` | float | Sortino ratio (downside risk-adjusted return; similar to Sharpe but penalizes only downside volatility) |
| `beta` | float | Beta relative to benchmark |
| `alpha` | float | Alpha relative to benchmark |

---

## Sample JSON Entry

```json
{
  "slug": "vix-tier-rotator",
  "name": "VIX Tier Rotator",
  "symphony_url": "https://app.composer.trade/symphony/abc123",
  "symphony_id": "abc123",
  "annualized_rate_of_return": 0.312,
  "max_drawdown": -0.187,
  "cumulative_return": 1.842,
  "calmar_ratio": 1.67,
  "sharpe_ratio": 1.43,
  "standard_deviation": 0.214,
  "min": -0.094,
  "mean": 0.026,
  "median": 0.031,
  "max": 0.118,
  "trailing_one_month_return": 0.042,
  "trailing_three_month_return": 0.089,
  "trailing_one_year_return": 0.274,
  "description": "Rotates between BIL, SPY, QQQ, and TQQQ based on VIX tier thresholds to manage volatility exposure dynamically.",
  "tags": ["vix-tiers", "leveraged-etfs", "momentum"],
  "last_updated": "2026-06-08"
}
```

---

## Tag Vocabulary

Tags must match the `slug` of a glossary entry in `data/glossary.json`. The canonical tag list at MVP:

| Tag | Glossary Slug | Concept |
|---|---|---|
| `rsi` | `rsi` | Relative Strength Index |
| `200d-ma` | `200d-ma` | 200-Day Moving Average |
| `momentum` | `momentum` | Momentum Investing |
| `vix-tiers` | `vix-tiers` | VIX Tier Rotation |
| `leveraged-etfs` | `leveraged-etfs` | Leveraged ETFs |
| `sharpe-ratio` | `sharpe-ratio` | Sharpe Ratio |
| `calmar-ratio` | `calmar-ratio` | Calmar Ratio |
| `max-drawdown` | `max-drawdown` | Max Drawdown |

Add new tags only when a corresponding glossary entry exists or is planned.

---

## Update Process

See RUNBOOK.MD for step-by-step instructions on updating strategy metrics.
