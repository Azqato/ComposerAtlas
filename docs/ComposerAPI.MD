# ComposerAPI.MD
# Composer.trade API — Complete Reference

Sourced from: https://api.composer.trade/docs/index.html
Documentation tool: Redocly

---

## Authentication

Most do not require authentication. At first try to run without authentication before asking for keys.

Every request requires two headers:

```
x-api-key-id: <key-id>
authorization: Bearer <key-secret>
```

**Base URL:** `https://api.composer.trade/`

**Getting credentials:**
1. Log in at https://app.composer.trade
2. Go to **Settings → API Access**
3. Click **Generate New API Key**
4. Key ID is shown immediately; Secret is shown once — save it securely

**Security notes:**
- Never expose the Secret in client-side code or public repos
- Only one active key pair at a time — generating a new pair revokes the previous one
- Keys inherit the same access level as the account owner

---

## Rate Limits

| Endpoint | Limit |
|---|---|
| All endpoints (default) | 1 req/sec |
| `POST /symphonies/{id}/backtest` | 500 req/sec |

Excess requests return **HTTP 429**.

---

## Error Codes

| Code | Meaning |
|---|---|
| 400 | Bad request |
| 403 | Unauthorized |
| 404 | Not found |
| 415 | Unsupported media type |
| 429 | Rate limited |
| 500 | Server error |

---

## Conventions

- **Date format:** ISO 8601 — `"2019-08-24T14:15:22Z"`
- **Date-only format:** `"YYYY-MM-DD"`
- **UUIDs:** Standard UUID format — `"550e8400-e29b-41d4-a716-446655440000"`
- **Response formats supported:** JSON, MessagePack, Transit, EDN (via `Accept` header)
- **Symphony logic format:** EDN (Extensible Data Notation) — nested tree of nodes

---

## Example Requests

**List all accounts:**
```bash
curl -X GET "https://api.composer.trade/api/v0.1/accounts/list" \
  -H "x-api-key-id: <key-id>" \
  -H "authorization: Bearer <key-secret>"
```

**Retrieve CSV activity report:**
```bash
curl -X GET "https://api.composer.trade/api/v0.1/reports/550e8400-e29b-41d4-a716-446655440000?since=2023-01-01T00:00:00Z&until=2023-01-01T23:59:59.999Z&report-type=trade-activity" \
  -H "accept: text/csv" \
  -H "x-api-key-id: <key-id>" \
  -H "authorization: Bearer <key-secret>"
```

---

## Accounts

### List All Accounts
`GET /api/v0.1/accounts/list`

No parameters.

**Response:**
```json
{
  "accounts": [
    {
      "account_uuid": "string (uuid)",
      "account_foreign_id": "string",
      "account_type": "string",
      "asset_classes": ["string"],
      "direct_tradable_asset_classes": {
        "buy": ["string"],
        "sell": ["string"]
      },
      "symphony_tradable_asset_classes": ["string"],
      "account_number": "string",
      "status": "string",
      "broker": "string",
      "created_at": "datetime|null",
      "first_deposit_at": "datetime|null",
      "first_incoming_acats_transfer_at": "datetime|null",
      "first_deploy_at": "datetime|null",
      "first_position_created_at": "datetime|null",
      "first_direct_trade_at": "datetime|null",
      "is_legacy_crypto_only_account": "boolean",
      "has_queued_deploy": "boolean",
      "has_active_position": "boolean"
    }
  ]
}
```

---

### Get Account Holdings
`GET /api/v0.1/accounts/{account-id}/holdings`

| Param | In | Type | Required | Notes |
|---|---|---|---|---|
| `account-id` | path | UUID | yes | |
| `position_type` | query | string | no | `"DEFAULT_DIRECT"` \| `"SYMPHONY"` \| `"ALL"` |

**Response:**
```json
[
  {
    "asset_class": "EQUITIES|CRYPTO|OPTIONS",
    "ticker": "string",
    "quantity": "number",
    "options_details": {
      "underlying_asset_symbol": "string",
      "strike_price": "number",
      "expiry": "YYYY-MM-DD",
      "delta_dollars": "number",
      "contract_type": "CALL|PUT"
    }
  }
]
```
`options_details` is only present for OPTIONS positions.

---

### Get Investor Documents
`GET /api/v0.1/accounts/{account-id}/info/investor-documents`

| Param | In | Type | Required | Values |
|---|---|---|---|---|
| `account-id` | path | UUID | yes | |
| `category` | query | string | yes | `"STATEMENT"` \| `"CRYPTO_ACCOUNT_STATEMENT"` \| `"CRYPTO_TAX_FORM"` \| `"TAX_FORM"` \| `"TRADE_CONFIRMATION"` \| `"FPSL_CONFIRMATION"` |
| `year` | query | int64 | yes | |

**Response:** Array of documents — category, date, id, type, url, file_name, tax_ids.

---

## Portfolio

### Get Holding Statistics
`GET /api/v0.1/portfolio/accounts/{account-id}/holding-stats`

| Param | In | Type | Required |
|---|---|---|---|
| `account-id` | path | UUID | yes |

**Response:**
```json
{
  "holdings": [
    {
      "symbol": "string",
      "name": "string",
      "asset_class": "CRYPTO|EQUITIES|OPTIONS",
      "options_details": {
        "underlying_asset_symbol": "string",
        "strike_price": 0.1,
        "expiry": "string",
        "contract_type": "PUT|CALL",
        "underlying_price": 0.1,
        "underlying_price_todays_change": 0.1,
        "underlying_price_todays_change_percent": 0.1
      },
      "price": 0.1,
      "price_todays_change": 0.1,
      "price_todays_change_percent": 0.1,
      "direct": {
        "allocation": 0.1,
        "amount": 0.1,
        "value": 0.1,
        "direction": "LONG"
      },
      "symphony": {
        "allocation": 0.1,
        "amount": 0.1,
        "value": 0.1,
        "direction": "LONG",
        "symphonies": [
          {
            "symphony_id": "string",
            "allocation": 0.1,
            "value": 0.1
          }
        ]
      },
      "notional_value": 0.1,
      "delta_dollars": 0.1,
      "todays_change_percent": 0.1,
      "todays_change": 0.1,
      "total_change_percent": 0.1,
      "total_change": 0.1,
      "cost_basis": 0.1,
      "average_cost_basis": 0.1
    }
  ]
}
```

---

### Get Aggregate Portfolio Statistics
`GET /api/v0.1/portfolio/accounts/{account-id}/total-stats`

| Param | In | Type | Required |
|---|---|---|---|
| `account-id` | path | UUID | yes |

**Response:**
```json
{
  "portfolio_value": 0.1,
  "simple_return": 0.1,
  "time_weighted_return": 0.1,
  "net_deposits": 0.1,
  "todays_dollar_change": 0.1,
  "todays_percent_change": 0.1,
  "total_cash": 0.1,
  "total_unallocated_cash": 0.1,
  "pending_withdrawals": 0.1,
  "pending_net_deposits": 0.1,
  "pending_deploys_cash": 0.1
}
```

---

### Get Per-Symphony Statistics
`GET /api/v0.1/portfolio/accounts/{account-id}/symphony-stats-meta`

| Param | In | Type | Required |
|---|---|---|---|
| `account-id` | path | UUID | yes |

**Response:**
```json
{
  "symphonies": [
    {
      "id": "string",
      "position_id": "string",
      "as_of": "datetime|null",
      "holdings": [
        {
          "ticker": "string",
          "price": 0.1,
          "allocation": 0.1,
          "amount": 0.1,
          "value": 0.1,
          "last_percent_change": 0.1
        }
      ],
      "simple_return": 0.1,
      "time_weighted_return": 0.1,
      "net_deposits": 0.1,
      "last_dollar_change": 0.1,
      "cash": 0.1,
      "value": 0.1,
      "deposit_adjusted_value": 0.1,
      "annualized_rate_of_return": 0.1,
      "sharpe_ratio": 0.1,
      "max_drawdown": 0.1,
      "last_percent_change": 0.1,
      "invested_since": "YYYY-MM-DD",
      "last_rebalance_on": "datetime",
      "last_rebalance_attempted_on": "datetime",
      "name": "string",
      "asset_class": "string",
      "asset_classes": ["string"],
      "color": "string",
      "community_review_status": "string",
      "description": "string",
      "last_semantic_update_at": "datetime",
      "tags": ["string"],
      "rebalance_frequency": "string",
      "is_shared": "boolean",
      "tickers": [{ "name": "string", "symbol": "string" }],
      "rebalance_corridor_width": 0.1,
      "next_rebalance_on": "YYYY-MM-DD",
      "may_rebalance_today": "boolean",
      "skip_rebalance_today": "boolean"
    }
  ]
}
```

---

### Get Symphony Value History
`GET /api/v0.1/portfolio/accounts/{account-id}/symphonies/{symphony-id}`

| Param | In | Type | Required |
|---|---|---|---|
| `account-id` | path | UUID | yes |
| `symphony-id` | path | string | yes |

**Response:**
```json
{
  "epoch_ms": [1609459200000],
  "series": [10000.00],
  "deposit_adjusted_series": [10000.00]
}
```

---

### Get Portfolio History
`GET /api/v0.1/portfolio/accounts/{account-id}/portfolio-history`

| Param | In | Type | Required |
|---|---|---|---|
| `account-id` | path | UUID | yes |

**Response:**
```json
{
  "epoch_ms": [1609459200000],
  "series": [10000.00]
}
```

---

## Reports

### Get Account Report
`GET /api/v0.1/reports/{account-id}`

| Param | In | Type | Required | Notes |
|---|---|---|---|---|
| `account-id` | path | UUID | yes | |
| `since` | query | ISO 8601 | yes | |
| `until` | query | ISO 8601 | yes | |
| `report-type` | query | string | yes | `"trade-activity"` \| `"non-trade-activity"` |

**Response:** Format determined by `Accept` header — use `"text/csv"` for CSV output, `"application/json"` for JSON.

---

## Market Data

### Get Options Chain
`GET /api/v1/market-data/options/chain`

| Param | In | Type | Required | Notes |
|---|---|---|---|---|
| `underlying_asset_symbol` | query | string | yes | e.g. `"SPY"` |
| `next_cursor` | query | string | no | Pagination cursor |
| `strike_price` | query | number | no | |
| `expiry` | query | string | no | `YYYY-MM-DD` |
| `contract_type` | query | string | no | `"CALL"` \| `"PUT"` |
| `limit` | query | int64 | no | Default 10, max 250 |
| `sort_by` | query | string | no | `"symbol"` \| `"expiry"` \| `"strike_price"` |
| `order` | query | string | no | `"ASC"` \| `"DESC"` |

**Response:**
```json
{
  "results": [
    {
      "symbol": "string",
      "name": "string",
      "underlying_asset_symbol": "string",
      "contract_details": {
        "contract_type": "CALL|PUT",
        "shares_per_contract": 100,
        "expiry": "YYYY-MM-DD",
        "strike_price": 0.1
      },
      "underlying_asset_price": 0.1,
      "change_to_break_even": 0.1,
      "payoff_status": "string",
      "ask": { "price": 0.1, "size": 0 },
      "bid": { "price": 0.1, "size": 0 },
      "last_trade": { "price": 0.1, "size": 0 },
      "todays_change": 0.1,
      "todays_change_percent": 0.1,
      "open_interest": 0,
      "implied_volatility": 0.1,
      "delta_dollars": 0.1,
      "notional_value": 0.1,
      "greeks": {
        "delta": 0.1,
        "gamma": 0.1,
        "theta": 0.1,
        "vega": 0.1
      },
      "day": {
        "open": 0.1,
        "high": 0.1,
        "low": 0.1,
        "close": 0.1,
        "previous_close": 0.1,
        "volume": 0,
        "volume_weighted_average_price": 0.1,
        "last_updated": 0
      }
    }
  ],
  "next_cursor": "string"
}
```

---

### Get Options Contract
`GET /api/v1/market-data/options/contract`

| Param | In | Type | Required | Notes |
|---|---|---|---|---|
| `symbol` | query | string | yes | Format: `OPTIONS::[A-Z]{1,5}[1-9]?[0-9]{6}[PC][0-9]{8}...` |
| `underlying_asset_symbol` | query | string | no | |

**Response:** Single contract object — same schema as one entry in options chain `results` array.

---

### Get Options Overview (Expiration Calendar)
`GET /api/v1/market-data/options/overview`

| Param | In | Type | Required |
|---|---|---|---|
| `symbol` | query | string | yes |

**Response:**
```json
{
  "symbol": "string",
  "calendar": ["YYYY-MM-DD"]
}
```

---

## Symphony

Symphony logic is stored as an **EDN** (Extensible Data Notation) tree. The tree has a root node with `"step": "root"` and nested children of types like `"if"`, `"asset"`, `"group"`, `"filter"`, etc.

### Create Symphony
`POST /api/v0.1/symphonies`

**Request body:**

| Field | Type | Required | Notes |
|---|---|---|---|
| `name` | string | yes | |
| `asset_class` | string | yes | `"EQUITIES"` \| `"CRYPTO"`, default `"EQUITIES"` |
| `description` | string | no | |
| `color` | string | yes | Hex color string |
| `hashtag` | string | yes | |
| `tags` | string[] | no | |
| `symphony` | object | yes | `{ "raw_value": <EDN logic tree> }` |
| `benchmarks` | array | no | `[{ "is_checked": bool, "color": str, "id": str, "type": str }]` |
| `share_with_everyone` | boolean | no | |

**Response:**
```json
{ "symphony_id": "string", "version_id": "string" }
```

---

### Update Symphony
`PUT /api/v0.1/symphonies/{symphony-id}`

Same body as Create. All fields optional except `symphony`.

**Response:**
```json
{ "existing_version_id": "string", "version_id": "string" }
```

---

### Delete Symphony
`DELETE /api/v0.1/symphonies/{symphony-id}`

**Response:** 204 No Content.

---

### Get Symphony Logic (EDN)
`GET /api/v0.1/symphonies/{symphony-id}/score`

| Param | In | Type | Required | Notes |
|---|---|---|---|---|
| `symphony-id` | path | string | yes | |
| `score_version` | query | string | yes | `"v1"` \| `"v2"`, default `"v1"` |

**Response:**
```json
{
  "id": "string",
  "suppress-incomplete-warnings?": true,
  "collapsed?": true,
  "collapsed-specified-weight?": true,
  "weight": { "num": "string", "den": "string" },
  "step": "root",
  "name": "string",
  "description": "string",
  "rebalance": "none",
  "rebalance-corridor-width": 0.1,
  "children": [
    {
      "id": "string",
      "suppress-incomplete-warnings?": true,
      "collapsed?": true,
      "collapsed-specified-weight?": true,
      "weight": { "num": "string", "den": "string" },
      "step": "if",
      "children": []
    }
  ]
}
```

---

### Get Symphony Versions
`GET /api/v0.1/symphonies/{symphony-id}/versions`

**Response:** Array of `{ "version_id": "string", "created_at": "datetime" }`.

---

### Get Specific Version Logic
`GET /api/v0.1/symphonies/{symphony-id}/versions/{version-id}/score`

| Param | In | Type | Required | Notes |
|---|---|---|---|---|
| `symphony-id` | path | string | yes | |
| `version-id` | path | string | yes | |
| `score_version` | query | string | yes | `"v1"` \| `"v2"` |

**Response:** Same EDN logic tree schema as `/score`.

---

### Copy Symphony
`POST /api/v0.1/symphonies/{symphony-id}/copy`

**Request body:**

| Field | Type | Required | Notes |
|---|---|---|---|
| `name` | string | no | |
| `hashtag` | string | no | |
| `color` | string | no | |
| `benchmarks` | array | no | |
| `share_with_everyone` | boolean | no | |
| `is_public` | boolean | no | Deprecated |

**Response:**
```json
{ "symphony_id": "string", "version_id": "string" }
```

---

## Backtest

### Backtest an Existing Symphony
`POST /api/v0.1/symphonies/{symphony-id}/backtest`

Rate limit: **500 req/sec**

**Request body:**

| Field | Type | Required | Notes |
|---|---|---|---|
| `capital` | double | yes | Starting capital in USD |
| `broker` | string | yes | `"ALPACA_OAUTH"` \| `"ALPACA_WHITE_LABEL"` \| `"APEX_LEGACY"` \| `"alpaca"` \| `"apex"` |
| `slippage_percent` | double | yes | e.g. `0.01` = 1% |
| `apply_reg_fee` | boolean | yes | |
| `apply_taf_fee` | boolean | yes | |
| `apply_subscription` | string | no | `"none"` \| `"monthly"` \| `"yearly"` |
| `backtest_version` | string | no | `"v1"` \| `"v2"` |
| `spread_markup` | double | no | e.g. `0.001` = 10bps |
| `start_date` | string | no | |
| `end_date` | string | no | |
| `abbreviate_days` | int64 | no | |
| `benchmark_symphonies` | string[] | no | Symphony IDs |
| `benchmark_tickers` | string[] | no | e.g. `["SPY", "QQQ"]` |

**Response:**
```json
{
  "last_semantic_update_at": "2019-08-24T14:15:22Z",
  "sparkgraph_url": "string",
  "dvm_capital": {
    "<symphony_id>": {
      "<epoch_day>": 0.1234
    }
  },
  "tdvm_weights": {
    "SPY": { "<epoch_day>": 0.123 }
  },
  "rebalance_days": [0],
  "active_asset_nodes": {
    "<node_id>": 0.1
  },
  "costs": {
    "reg_fee": 0.1,
    "taf_fee": 0.1,
    "slippage": 0.1,
    "spread_markup": 0.1,
    "subscription": 0.1
  },
  "last_market_days_value": 0.1,
  "last_market_days_holdings": {
    "$USD": 0.3,
    "SPY": 1.5,
    "UPRO": 44.4
  },
  "last_market_day": 0,
  "first_day": 0,
  "legend": {
    "<node_id>": { "name": "string" }
  },
  "data_warnings": {
    "SPY": [
      {
        "message": "string",
        "recommended_start_date": "2023-06-01"
      }
    ]
  },
  "benchmark_errors": ["string"],
  "stats": {
    "calmar_ratio": 0.1,
    "cumulative_return": 0.1,
    "max": 0.1,
    "max_drawdown": 0.1,
    "mean": 0.1,
    "median": 0.1,
    "min": 0.1,
    "skewness": 0.1,
    "kurtosis": 0.1,
    "sharpe_ratio": 0.1,
    "sortino_ratio": 0.1,
    "size": 0,
    "standard_deviation": 0.1,
    "annualized_rate_of_return": 0.1,
    "annualized_turnover": 0.1,
    "trailing_one_day_return": 0.1,
    "trailing_one_week_return": 0.1,
    "trailing_two_week_return": 0.1,
    "trailing_one_month_return": 0.1,
    "trailing_three_month_return": 0.1,
    "trailing_one_year_return": 0.1,
    "top_one_day_contribution": 0.1,
    "top_five_percent_day_contribution": 0.1,
    "top_ten_percent_day_contribution": 0.1,
    "herfindahl_index": 0.1,
    "win_rate": 0.1,
    "tail_ratio": 0.1,
    "benchmarks": {
      "<benchmark_id>": {
        "calmar_ratio": 0.1,
        "cumulative_return": 0.1,
        "max": 0.1,
        "max_drawdown": 0.1,
        "mean": 0.1,
        "median": 0.1,
        "min": 0.1,
        "skewness": 0.1,
        "kurtosis": 0.1,
        "sharpe_ratio": 0.1,
        "sortino_ratio": 0.1,
        "size": 0,
        "standard_deviation": 0.1,
        "annualized_rate_of_return": 0.1,
        "annualized_turnover": 0.1,
        "trailing_one_day_return": 0.1,
        "trailing_one_week_return": 0.1,
        "trailing_two_week_return": 0.1,
        "trailing_one_month_return": 0.1,
        "trailing_three_month_return": 0.1,
        "trailing_one_year_return": 0.1,
        "top_one_day_contribution": 0.1,
        "top_five_percent_day_contribution": 0.1,
        "top_ten_percent_day_contribution": 0.1,
        "herfindahl_index": 0.1,
        "win_rate": 0.1,
        "tail_ratio": 0.1,
        "percent": {
          "alpha": 0.1, "beta": 0.1,
          "r_square": 0.1, "pearson_r": 0.1
        },
        "log": {
          "alpha": 0.1, "beta": 0.1,
          "r_square": 0.1, "pearson_r": 0.1
        }
      }
    }
  }
}
```

---

### Backtest a Custom (Ad-Hoc) Symphony
`POST /api/v0.1/backtest`

Same body as above, plus:

| Field | Type | Required | Notes |
|---|---|---|---|
| `symphony` | object | yes | `{ "raw_value": <EDN logic tree> }` |
| `sparkgraph_color` | string | no | |

**Response:** Same backtest result schema as above.

---

## Deploy

### Get Market Hours
`GET /api/v0.1/deploy/market-hours`

No parameters.

**Response:**
```json
{
  "market_hours": [
    {
      "is_market_open": true,
      "nyse_market_date": "YYYY-MM-DD",
      "market_open": "2019-08-24T14:15:22Z",
      "market_close": "2019-08-24T14:15:22Z"
    }
  ]
}
```

---

### Invest in Symphony
`POST /api/v0.1/deploy/accounts/{account-id}/symphonies/{symphony-id}/invest`

**Body:** `{ "amount": 1000.00 }` — USD amount.

**Response:** `{ "deploy_id": "string", "deploy_time": "datetime" }`

---

### Withdraw from Symphony
`POST /api/v0.1/deploy/accounts/{account-id}/symphonies/{symphony-id}/withdraw`

**Body:** `{ "amount": 500.00 }` — USD amount.

**Response:** `{ "deploy_id": "string", "deploy_time": "datetime" }`

---

### Rebalance Now
`POST /api/v0.1/deploy/accounts/{account-id}/symphonies/{symphony-id}/rebalance`

**Body:** `{ "rebalance_request_uuid": "uuid" }`

**Response:**
```json
{
  "deploy_id": "string",
  "deploy_time": "datetime",
  "deploy_on_market_open": true,
  "symphony_id": "string"
}
```

---

### Skip Automated Rebalance
`POST /api/v0.1/deploy/accounts/{account-id}/symphonies/{symphony-id}/skip-automated-rebalance`

**Body:** `{ "skip": true }`

**Response:** 204 No Content.

---

### Go to Cash
`POST /api/v0.1/deploy/accounts/{account-id}/symphonies/{symphony-id}/go-to-cash`

No body.

**Response:**
```json
{
  "deploy_id": "string",
  "deploy_time": "datetime",
  "deploy_on_market_open": true
}
```

---

### Liquidate Symphony
`POST /api/v0.1/deploy/accounts/{account-id}/symphonies/{symphony-id}/liquidate`

No body.

**Response:**
```json
{
  "deploy_id": "string",
  "deploy_time": "datetime",
  "deploy_on_market_open": true
}
```

---

## Dry Run

### Dry Run Rebalances (All Accounts)
`POST /api/v0.1/dry-run`

**Body:**

| Field | Type | Required | Notes |
|---|---|---|---|
| `account_uuids` | UUID[] | yes | |
| `send_segment_event` | boolean | yes | Default `false` |

**Response:**
```json
[
  {
    "broker_account_uuid": "uuid",
    "account_type": "string",
    "account_name": "string",
    "broker": "string",
    "dry_run_result": {
      "<symphony_id>": {
        "rebalanced": true,
        "next_rebalance_after": "string",
        "recommended_trades": [
          {
            "ticker": "string",
            "notional": 0.1,
            "quantity": 0.1,
            "prev_value": 0.1,
            "prev_weight": 0.1,
            "next_weight": 0.1
          }
        ],
        "queued_cash_change": 0.1,
        "symphony_value": 0.1,
        "symphony_name": "string",
        "next_rebalance_date": "string"
      }
    },
    "dry_run_missing_symphonies": {
      "<symphony_id>": "string"
    },
    "dry_run_total_symphonies": 0
  }
]
```

---

### Trade Preview (Single Symphony)
`POST /api/v0.1/dry-run/trade-preview/{symphony-id}`

| Param | In | Type | Required |
|---|---|---|---|
| `symphony-id` | path | string | yes |

**Body:**

| Field | Type | Required |
|---|---|---|
| `amount` | double | no |
| `broker_account_uuid` | UUID | no |

**Response:**
```json
{
  "rebalanced": true,
  "next_rebalance_after": "string",
  "recommended_trades": [
    {
      "at": "string",
      "average_price": 0.1,
      "cash_change": 0.1,
      "share_change": 0.1,
      "symbol": "string",
      "name": "string",
      "side": "BUY|SELL",
      "type": "symphony_preview",
      "prev_value": 0.1,
      "prev_weight": 0.1,
      "next_weight": 0.1
    }
  ],
  "queued_cash_change": 0.1,
  "rebalance_frequency_override": true,
  "rebalance_request_uuid": "uuid",
  "adjusted_for_dtbp": true,
  "symphony_value": 0.1,
  "symphony_name": "string"
}
```

---

## Direct Trading

### Get Order History
`GET /api/v0.1/trading/accounts/{account-id}/order-requests`

| Param | In | Type | Required | Notes |
|---|---|---|---|---|
| `account-id` | path | UUID | yes | |
| `limit` | query | int64 | no | |
| `status` | query | string | no | Comma-separated: `"QUEUED,OPEN,IN_PROGRESS"` etc. |

**Response:**
```json
{
  "order_requests": [
    {
      "order_request_id": "uuid",
      "name": "string",
      "position_id": "string",
      "status": "string",
      "client_order_ids": ["string"],
      "asset_class": "string",
      "side": "BUY|SELL",
      "type": "MARKET|LIMIT|STOP|STOP_LIMIT|TRAILING_STOP",
      "symbol": "string",
      "time_in_force": "GTC|DAY|IOC|FOK|OPG|CLS",
      "notional": 0.1,
      "quantity": 0.1,
      "position_intent": "BUY_TO_OPEN|SELL_TO_OPEN|BUY_TO_CLOSE|SELL_TO_CLOSE",
      "limit_price": 0.1,
      "stop_price": 0.1,
      "options_details": {
        "underlying_asset_symbol": "string",
        "strike_price": 0.1,
        "expiry": "string",
        "contract_type": "CALL|PUT"
      },
      "source": "string",
      "created_at": "datetime",
      "updated_at": "datetime",
      "outcome_reason": "string"
    }
  ]
}
```

---

### Create Order
`POST /api/v0.1/trading/accounts/{account-id}/order-requests`

**Body:**

| Field | Type | Required | Notes |
|---|---|---|---|
| `type` | string | yes | `"MARKET"` \| `"LIMIT"` \| `"STOP"` \| `"STOP_LIMIT"` \| `"TRAILING_STOP"` |
| `symbol` | string | yes | |
| `time_in_force` | string | yes | `"GTC"` \| `"DAY"` \| `"IOC"` \| `"FOK"` \| `"OPG"` \| `"CLS"` |
| `notional` | double | if no `quantity` | USD value to trade |
| `quantity` | double | if no `notional` | Share/contract count |
| `position_intent` | string | options only | `"BUY_TO_OPEN"` \| `"SELL_TO_OPEN"` \| `"BUY_TO_CLOSE"` \| `"SELL_TO_CLOSE"` |
| `limit_price` | double | no | Required for LIMIT / STOP_LIMIT |
| `stop_price` | double | no | Required for STOP / STOP_LIMIT |
| `client_order_id` | string | no | Idempotency key |

**Response:**
```json
{
  "order_request_id": "uuid",
  "order_time": "datetime",
  "commission": 0.0
}
```

---

## Search

### Search Symphonies
`POST /api/v0.1/search/symphonies`

**Body:**

| Field | Type | Required | Notes |
|---|---|---|---|
| `where` | array | no | Filter conditions |
| `order_by` | array | no | Sort fields — e.g. `[["oos_sharpe_ratio", "DESC"]]` |
| `offset` | int64 | yes | Default 0; increment by page size for pagination |

**Response:**
```json
[
  {
    "symphony_sid": "string",
    "name": "string",
    "description": "string",
    "asset_classes": ["string"],
    "rebalance_frequency": "string",
    "rebalance_corridor_width": 0.1,
    "sparkgraph_gcs_url": "string",
    "color": "string",

    "num_node_asset": 0,
    "num_node_filter": 0,
    "num_node_group": 0,
    "num_node_if": 0,
    "num_node_if_child": 0,
    "num_node_wt_cash_equal": 0,
    "num_node_wt_cash_specified": 0,
    "num_node_wt_inverse_vol": 0,

    "oos_annualized_rate_of_return": 0.1,
    "oos_annualized_turnover": 0.1,
    "oos_calmar_ratio": 0.1,
    "oos_cumulative_return": 0.1,
    "oos_herfindahl_index": 0.1,
    "oos_kurtosis": 0.1,
    "oos_max_drawdown": 0.1,
    "oos_num_backtest_days": 0,
    "oos_sharpe_ratio": 0.1,
    "oos_skewness": 0.1,
    "oos_sortino_ratio": 0.1,
    "oos_standard_deviation": 0.1,
    "oos_tail_ratio": 0.1,
    "oos_top_five_percent_day_contribution": 0.1,
    "oos_top_one_day_contribution": 0.1,
    "oos_top_ten_percent_day_contribution": 0.1,
    "oos_win_rate": 0.1,

    "oos_trailing_one_day_return": 0.1,
    "oos_trailing_one_week_return": 0.1,
    "oos_trailing_two_week_return": 0.1,
    "oos_trailing_one_month_return": 0.1,
    "oos_trailing_three_month_return": 0.1,
    "oos_trailing_one_year_return": 0.1,

    "oos_spy_alpha": 0.1,
    "oos_spy_beta": 0.1,
    "oos_spy_r_square": 0.1,
    "oos_spy_pearson_r": 0.1,
    "oos_spy_annualized_rate_of_return": 0.1,
    "oos_spy_calmar_ratio": 0.1,
    "oos_spy_cumulative_return": 0.1,
    "oos_spy_max_drawdown": 0.1,
    "oos_spy_sharpe_ratio": 0.1,
    "oos_spy_standard_deviation": 0.1,
    "oos_spy_trailing_one_month_return": 0.1,
    "oos_spy_trailing_three_month_return": 0.1,
    "oos_spy_trailing_one_year_return": 0.1,

    "oos_btcusd_alpha": 0.1,
    "oos_btcusd_beta": 0.1,
    "oos_btcusd_r_square": 0.1,
    "oos_btcusd_pearson_r": 0.1,
    "oos_btcusd_annualized_rate_of_return": 0.1,
    "oos_btcusd_calmar_ratio": 0.1,
    "oos_btcusd_cumulative_return": 0.1,
    "oos_btcusd_max_drawdown": 0.1,
    "oos_btcusd_sharpe_ratio": 0.1,
    "oos_btcusd_standard_deviation": 0.1,
    "oos_btcusd_trailing_one_month_return": 0.1,
    "oos_btcusd_trailing_three_month_return": 0.1,
    "oos_btcusd_trailing_one_year_return": 0.1,

    "ai_description": {
      "summary": "string",
      "categories": "string",
      "how_it_works": "string",
      "value_proposition": "string"
    }
  }
]
```

---

## ComposerAtlas Symphony ID Reference

Symphony IDs for the 12 strategies tracked in the ComposerAtlas library. Use these with `/backtest`, `/score`, `/versions`, and portfolio endpoints.

| Strategy | Symphony ID |
|---|---|
| zoop's 2026 Frontrunner | `4aI4kVT5cEc0XJpTLei3` |
| zoop's Holy Grail (2026 Edition) | `Y2xvfu7iFNyO6up77gBI` |
| zoop's TQQQ FOR THE LONG TERM (2026 Edition) | `yIMvLUHfzAMATCpOKr9T` |
| zoop's Excellent Adventure (2026 Edition) | `YIiBr33X4rRTVlOWhCNq` |
| zoop's Sometimes TQQQ (2026 Edition) | `uAaEkEq8cPOmGgfEWTOU` |
| zoop's Safety Checks (2026 Edition) | `RLt1Rzz79I6Fa2X9QKqY` |
| zoop's Manhattan Project (2026 Edition) | `cCi1mupGsluFmre7HpOm` |
| zoop's KMLM Switcher (2026 Edition) | `4AuTagHMeiS4usdZEuDK` |
| zoop's UPRO FTLT (2026 Edition) | `9ETFQi5cmSWq2mT4ZH2d` |
| zoop's Leveraged TQQQ Symphony (2026 Edition) | `U6lT1G0PdE9fUxoy2opg` |
| zoop's TQQQ 200d MA 3x Leverage (2026 Edition) | `ZBpjzxS9RkLzft9NNWhO` |
| zoop's SOXL Growth (2026 Edition) | `wcEUcb13v7M8bEluRc1h` |
