# DESIGN.MD - Design System
# ComposerAtlas

**Version:** 1.2
**Status:** Active
**Last Updated:** 2026-06-08

---

## Philosophy

ComposerAtlas uses a dark, editorial visual language inspired by Composer.trade's interface. It pairs the energy and color vocabulary of Composer with the structured, wiki-style readability of a reference document. The result is a site that feels authoritative and trustworthy without being sterile.

Every visual decision serves the user's ability to understand information. Color is used semantically. Typography is used for hierarchy. Whitespace is not wasted. Nothing is decorative for its own sake.

---

## Color Palette

Inspired directly by Composer.trade's design system.

| Token | Hex | Usage |
|---|---|---|
| `--color-bg` | `#0d0d0d` | Page background |
| `--color-surface` | `#141414` | Card / panel background |
| `--color-surface-raised` | `#1a1a1a` | Elevated surface, hover state |
| `--color-border` | `#1f1f1f` | Default borders, dividers |
| `--color-border-hover` | `#2e2e2e` | Border on hover |
| `--color-primary` | `#f0f0f0` | Body text, headings |
| `--color-secondary` | `#b0b0b0` | Labels, captions, metadata, muted text |
| `--color-disabled` | `#444444` | Disabled states, truly muted/inactive elements |
| `--color-green` | `#00e676` | Positive returns, CTAs, active nav, highlights |
| `--color-pink` | `#ff4d8d` | Negative returns, max drawdown, warnings |
| `--color-pink-muted` | `#ff4d8d20` | Pink at 12% opacity — warning backgrounds |
| `--color-blue` | `#4d9fff` | Links, interactive element hover borders |
| `--color-yellow` | `#f5c518` | Caution indicators, median values |
| `--color-purple` | `#a78bfa` | Glossary tags, secondary accents |
| `--color-purple-muted` | `#a78bfa20` | Purple at 12% opacity — tag backgrounds |
| `--color-black` | `#000000` | Deep backgrounds, CTA button text |

### Semantic Color Rules

- **Green** — used exclusively for positive values and primary CTAs. Never decorative.
- **Pink** — used exclusively for negative values (drawdown, losses). Never decorative.
- **Purple** — reserved for glossary / concept vocabulary only.
- **Blue** — reserved for interactive states (links, hover borders). Not used for data values.
- **Yellow** — used for neutral caution signals (data freshness warnings, median indicators).

---

## Typography

- **Primary Font:** Inter (Google Fonts) — body text, UI labels, headings
- **Monospace Font:** JetBrains Mono (Google Fonts, fallback: Fira Code) — metric values, strategy IDs, code

### Type Scale

| Element | Size | Weight | Line Height |
|---|---|---|---|
| H1 (page title) | 2.25rem / 36px | 700 | 1.2 |
| H2 (section heading) | 1.75rem / 28px | 600 | 1.3 |
| H3 (subsection) | 1.375rem / 22px | 600 | 1.35 |
| H4 (label heading) | 1.125rem / 18px | 500 | 1.4 |
| Body | 1rem / 16px | 400 | 1.6 |
| Small / Caption | 0.875rem / 14px | 400 | 1.5 |
| Metric value | 1rem / 16px | 500 | 1.4 (mono) |
| Tag / Badge | 0.75rem / 12px | 500 | 1 |

### Font Loading

```html
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet" />
```

---

## Responsive Breakpoints

Mobile-first. All base styles target the smallest viewport; utilities add complexity at larger sizes.

| Name | Min Width | Description |
|---|---|---|
| (base) | 0px | Single-column, mobile layout |
| `sm` | 640px | Large mobile / small tablet |
| `md` | 768px | Tablet |
| `lg` | 1024px | Small desktop |
| `xl` | 1280px | Standard desktop |
| `2xl` | 1536px | Wide desktop (content still capped at 1200px) |

Max content width: **1200px**, centered with `mx-auto px-4 md:px-6 lg:px-8`.

---

## Spacing Scale

4px base unit. All spacing values are multiples of 4.

| Token | Value | Usage |
|---|---|---|
| `space-1` | 4px | Inline icon gap, tight padding |
| `space-2` | 8px | Tag padding, tight element gap |
| `space-3` | 12px | Card padding (mobile) |
| `space-4` | 16px | Card padding (desktop), section gaps |
| `space-6` | 24px | Component separation |
| `space-8` | 32px | Section padding |
| `space-12` | 48px | Major section gaps |
| `space-16` | 64px | Page-level vertical rhythm |
| `space-24` | 96px | Hero section padding |

---

## Border Radius

| Context | Value |
|---|---|
| Cards, panels | 8px |
| Buttons | 6px |
| Tags / badges | 4px |
| Inputs | 6px |
| Tooltips | 4px |

---

## Elevation / Depth

Elevation is expressed through border color changes, not traditional box-shadows, to stay consistent with the dark theme.

| Level | Description | Implementation |
|---|---|---|
| 0 (flat) | Default page background | `bg-bg` |
| 1 (surface) | Card, panel | `bg-surface` + `border border-border` |
| 2 (raised) | Hover state, active dropdown | `bg-surface-raised` + `border border-border-hover` |

Subtle `box-shadow` is acceptable on hover states: `0 4px 24px rgba(0,0,0,0.4)`.

---

## Layout

### Page Shell

```
┌──────────────────────────────────────────────────────┐
│  [Nav — fixed, full width, dark surface]             │
├──────────────────────────────────────────────────────┤
│                                                      │
│   [Main content — max-w-content, centered]           │
│                                                      │
│                                                      │
├──────────────────────────────────────────────────────┤
│  [Footer]                                            │
└──────────────────────────────────────────────────────┘
```

Main content area has `pt-16` (64px) top padding to clear the fixed nav.

---

## Page Layouts

### Home Page (`/`)

```
Nav
────────────────────────────────────────────────────
Hero Section
  H1: "ComposerAtlas"
  Subtitle: 1-2 sentence description of the site
  Stats Bar: "12 Strategies  ·  8 Concepts  ·  Updated Jun 8, 2026"
────────────────────────────────────────────────────
Strategy Grid (3-col desktop / 2-col tablet / 1-col mobile)
  [ StrategyCard ] [ StrategyCard ] [ StrategyCard ]
  [ StrategyCard ] [ StrategyCard ] [ StrategyCard ]
  ...
────────────────────────────────────────────────────
Footer
```

- Hero: `py-16` vertical padding, text centered on mobile / left-aligned on lg+
- Stats bar: secondary text color, monospaced numbers, `·` separators
- Grid: `gap-6`, cards equal height within rows

---

### Strategy Detail Page (`/strategies/[slug]`)

```
Nav
────────────────────────────────────────────────────
Breadcrumb
  Home › Strategies › [Strategy Name]
────────────────────────────────────────────────────
Strategy Header
  H1: Strategy Name
  [tag] [tag] [tag]   ← glossary tag chips
  Short description (1-3 sentences)
  [Open in Composer →]  ← CTA button
────────────────────────────────────────────────────
Two-Column Body (desktop: 8/4 col split; mobile: stacked)
┌──────────────────────────┐  ┌────────────────────┐
│ H2: How It Works         │  │ Metrics            │
│ Long-form logic content  │  │ ─────────────────  │
│                          │  │ Ann. Return  31.2% │
│ H3: Signals Used         │  │ Max Drawdown -18.7%│
│ [RSI] [VIX Tiers]        │  │ Sharpe Ratio  1.43 │
│                          │  │ ...               │
│ H3: Risk Profile         │  │                   │
│ Risk summary box         │  │ Last updated:     │
│                          │  │ Jun 8, 2026       │
└──────────────────────────┘  └────────────────────┘
  (metrics sidebar is sticky on desktop scroll)
────────────────────────────────────────────────────
Footer
```

**Mobile behavior:** Metrics table renders above the logic section (stacks vertically: header → metrics → logic).

---

### Glossary Index (`/glossary/`)

```
Nav
────────────────────────────────────────────────────
Page Header
  H1: "Glossary"
  Subtitle: "Reference definitions for concepts used in ComposerAtlas strategies."
────────────────────────────────────────────────────
Category Filter Bar (MVP: static; V2: interactive filter)
  [All] [Indicators] [Risk Metrics] [Asset Classes] [Strategy Concepts]
────────────────────────────────────────────────────
Concept Card Grid (3-col desktop / 2-col tablet / 1-col mobile)
  [ ConceptCard ] [ ConceptCard ] [ ConceptCard ]
  [ ConceptCard ] [ ConceptCard ] [ ConceptCard ]
  ...
────────────────────────────────────────────────────
Footer
```

---

### Glossary Detail Page (`/glossary/[slug]`)

```
Nav
────────────────────────────────────────────────────
Breadcrumb
  Home › Glossary › [Concept Name]
────────────────────────────────────────────────────
Concept Header
  H1: Concept Name
  [Category tag]  e.g. [Indicator]
────────────────────────────────────────────────────
Two-Column Body (desktop: 8/4 col split; mobile: stacked)
┌──────────────────────────┐  ┌────────────────────┐
│ Definition               │  │ Strategies Using   │
│ Body text                │  │ This Concept       │
│                          │  │ ─────────────────  │
│ H3: How It Works         │  │ [CompactCard]      │
│ Body text                │  │ [CompactCard]      │
│                          │  │ [CompactCard]      │
│ H3: In Practice          │  │                   │
│ Body text                │  │                   │
│                          │  │                   │
│ H3: Formula (if any)     │  │                   │
│ Code block               │  │                   │
│                          │  │                   │
│ H3: Limitations          │  │                   │
│ Body text                │  │                   │
└──────────────────────────┘  └────────────────────┘
────────────────────────────────────────────────────
Footer
```

---

### About Page (`/about/`)

Single-column, centered, max-width 768px. Sections:
1. What is ComposerAtlas?
2. Who curates it?
3. Independence and integrity statement
4. How to support
5. Disclaimer (not financial advice)

---

## Navigation

### Desktop Nav (lg+)

```
┌────────────────────────────────────────────────────────┐
│ [◈ ComposerAtlas]    Strategies  Glossary  About    [Support ↗] │
└────────────────────────────────────────────────────────┘
```

- Fixed to top of viewport; `z-50`
- Background: `--color-surface` with `border-b border-border`
- Height: 56px
- Logo: white text, bold, no icon at MVP
- Nav links: `--color-secondary` default; `--color-primary` + green underline on active route
- Support button: bg `--color-green`, text `--color-black`, bold, 6px border-radius, opens in new tab
- Hover transition on links: 150ms color change

### Mobile Nav (< lg)

```
┌────────────────────────────────────────────────────────┐
│ [◈ ComposerAtlas]                              [☰]    │
└────────────────────────────────────────────────────────┘
```

Hamburger (☰) opens a full-height slide-in drawer from the right:
```
┌──────────────────────┐
│              [✕]     │
├──────────────────────┤
│ Strategies           │
│ Glossary             │
│ About                │
├──────────────────────┤
│ [Support ↗]          │
└──────────────────────┘
```

- Drawer: `bg-surface`, full viewport height, `w-72` (288px), slides in 200ms ease-out
- Overlay: semi-transparent black backdrop, click-to-close
- Nav links: large text (18px), `py-4` padding, full-width tap targets
- Toggle handled by a vanilla JS event listener on `#nav-toggle` — no framework required

---

## Components

### StrategyCard

Used on the home page strategy grid.

```
┌──────────────────────────────────────────┐
│ Strategy Name                     H3     │
│ Short description, 1-2 lines      body   │
│                                          │
│ Ann. Return    Max Drawdown  Sharpe      │
│  +31.2%         -18.7%       1.43        │
│  (green)        (pink)       (primary)   │
│                                          │
│ [vix] [leveraged-etf]    [View Strategy →]│
└──────────────────────────────────────────┘
```

**States:**
- Default: `bg-surface`, `border border-border`, 8px radius
- Hover: `border-color` transitions to `--color-blue`, `box-shadow: 0 4px 24px rgba(0,0,0,0.4)`, `bg-surface-raised`
- Focus (keyboard): same as hover + 2px `outline` in `--color-blue`
- Transition: `border-color 150ms ease, box-shadow 150ms ease, background 150ms ease`
- The entire card is a clickable link wrapping the content

**"View Strategy →" link:** text in `--color-green`, no button styling — subtle inline link
**Metric values:** monospace font; positive in green, negative in pink, neutral (Sharpe) in primary

---

### StrategyCardCompact

Slim variant used in glossary page sidebars. Single row.

```
┌──────────────────────────────────────────┐
│ Strategy Name              +31.2% ARR    │
└──────────────────────────────────────────┘
```

- No description, no tags, just name + key metric + arrow link
- Same hover behavior as StrategyCard

---

### MetricsTable

Used on strategy detail pages.

```
┌──────────────────────────────────────────┐
│  Ann. Return          +31.2%             │
│  Max Drawdown         -18.7%             │
│  Cumulative Return    +184.2%            │
│  Calmar Ratio          1.67              │
│  Sharpe Ratio          1.43              │
│  Std. Deviation        21.4%             │
│  ──────────────────────────────          │
│  1-Month Return        +4.2%             │
│  3-Month Return        +8.9%             │
│  1-Year Return         +27.4%            │
│  ──────────────────────────────          │
│  Min Period Return     -9.4%             │
│  Mean Period Return    +2.6%             │
│  Median Period Return  +3.1%             │
│  Max Period Return     +11.8%            │
│  ──────────────────────────────          │
│  Last Updated          Jun 8, 2026       │
└──────────────────────────────────────────┘
```

- Metric labels: `--color-secondary`, 14px
- Values: monospace, 16px, color-coded (see METRICS.MD display guidelines)
- Two-column layout: label left, value right-aligned
- Horizontal rule dividers between logical groups
- No table borders — clean dividers only

---

### GlossaryTag

Pill badge linking to a glossary concept page. Used on strategy pages.

```
 ┌──────────────┐
 │  vix-tiers   │
 └──────────────┘
```

- `border border-purple`, `text-purple`, `bg-transparent`, 4px radius
- Padding: `px-2 py-0.5` (8px / 2px)
- Font: 12px, 500 weight
- Hover: `bg-purple-muted` (purple at 12% opacity)
- Transition: `background 100ms ease`
- Renders as `<a href="/glossary/[tag]">` — always a link

---

### CTAButton — "Open in Composer"

```
 ┌─────────────────────────────────┐
 │  Open in Composer  ↗            │
 └─────────────────────────────────┘
```

- Background: `--color-green`; text: `--color-black`; 700 weight; 16px
- Padding: `px-6 py-3` (24px / 12px)
- Border radius: 6px
- Width: `w-full` on mobile; `w-auto` inline on sm+
- Hover: `brightness-110` filter (slightly lighter green)
- Active: `brightness-90` (slightly darker)
- `target="_blank" rel="noopener noreferrer"` (required)
- Transition: `filter 100ms ease`

---

### ConceptCard

Used on the glossary index.

```
┌──────────────────────────────────────────┐
│ [Indicator]                              │
│ RSI                               H3     │
│ Relative Strength Index. A momentum     │
│ oscillator measuring speed of price...  │
│                                          │
│ Used in 4 strategies    [Learn More →]   │
└──────────────────────────────────────────┘
```

- Category tag: color by category — Indicator: blue, Risk Metric: pink, Asset Class: yellow, Strategy Concept: purple
- "Used in X strategies" — count computed at runtime from strategies.json tags
- Same hover behavior as StrategyCard (blue border lift)

---

### Footer

Full-width centered bar at the bottom of every page. Rendered by `renderFooter()` in `js/app.js` into `<footer id="footer-root">`.

```
────────────────────────────────────────────────────
Strategies  Glossary  About  Support  Composer.trade ↗
Not affiliated with Composer Technologies, Inc. All metrics are backtested...
© 2026 ComposerAtlas · Built by Azqato
────────────────────────────────────────────────────
```

- **CSS selector:** `footer` element (not a class) — matches `<footer id="footer-root">`
- `padding: 2rem`, `text-align: center`, `border-top: 1px solid --color-border`
- `.footer-links`: `display:flex; flex-wrap:wrap; justify-content:center; gap:16px`
- Link font size: 0.8rem, `--color-secondary`, hover transitions to `--color-primary`
- `.footer-legal`: 0.75rem, `--color-secondary`, `max-width:560px`, centered
- `.footer-copy`: 0.75rem, `--color-secondary`; "Built by" link uses `--color-blue`

---

### Breadcrumb

Single-line navigation trail, rendered above page headers on all sub-pages.

```
Home › Strategies › VIX Tier Rotator
```

- Font: 14px, `--color-secondary`
- Separators: `›` in secondary text
- All items except the current page are links in `--color-blue`
- Current page item: `--color-primary`, not a link

---

## Iconography

- Icon set: **Lucide** (outline style, tree-shakeable)
- Icon size: 16px inline with text, 20px standalone
- Icons never used as the sole affordance — always paired with text or a tooltip

---

## Loading States

All data is loaded asynchronously at runtime via `window.STRATEGIES_DATA` globals (for `file://`) or `fetch()` (for HTTP). Pages show a spinner while data loads:

- **Spinner:** animated CSS border spinner rendered inside the grid container before data arrives
- **Skeleton card (future):** animated shimmer using `--color-border` as base, `--color-surface-raised` as highlight
- Shimmer animation: `background: linear-gradient(90deg, ...)` swept left-to-right, 1.5s infinite
- Skeleton dimensions should match the real component to prevent layout shift (CLS = 0)

---

## Error States

- **JSON parse failure at runtime:** If `strategies.json` or `glossary.json` is malformed, `loadStrategies()` / `loadGlossary()` will throw. Pages catch this and render an error message in place of the grid.
- **Broken Composer link (external):** No in-page error state for external links. A periodic manual audit process handles broken URLs (see RUNBOOK.MD).
- **404 page:** A custom `404.html` exists with the site nav intact and a link back to the strategy index. GitHub Pages serves it automatically for unmatched routes.

---

## Motion and Transitions

Keep motion minimal and purposeful. No page transitions at MVP.

| Element | Property | Duration | Easing |
|---|---|---|---|
| Cards (hover) | `border-color`, `background`, `box-shadow` | 150ms | ease |
| CTA button | `filter` (brightness) | 100ms | ease |
| Tags (hover) | `background` | 100ms | ease |
| Mobile nav drawer | `transform` (slide in) | 200ms | ease-out |
| Mobile nav overlay | `opacity` | 150ms | ease |
| Nav links | `color` | 150ms | ease |

All transitions respect `prefers-reduced-motion`:
```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after { transition: none !important; }
}
```

---

## Accessibility

Target: **WCAG 2.1 Level AA**

### Contrast Requirements
- Body text on background: minimum 4.5:1 (WCAG AA)
  - `#f0f0f0` on `#0d0d0d` = ~15:1 ✓
  - `#b0b0b0` on `#0d0d0d` = ~9.4:1 ✓
  - `#00e676` on `#0d0d0d` = ~8.4:1 ✓
  - `#ff4d8d` on `#0d0d0d` = ~4.6:1 ✓ (minimum; verify with production values)
  - `#000000` on `#00e676` (CTA button) = ~15:1 ✓

### Focus Management
- All interactive elements have visible focus rings: `outline: 2px solid #4d9fff; outline-offset: 2px`
- Tab order follows visual reading order
- Mobile nav drawer traps focus while open; returns focus to hamburger on close

### Semantic HTML
- One `<h1>` per page
- Proper heading hierarchy (no skipping levels)
- Strategy cards use `<article>` elements
- Nav uses `<nav aria-label="Main navigation">`
- Breadcrumb uses `<nav aria-label="Breadcrumb">` with `aria-current="page"` on current item
- Metrics table uses `<dl>` (definition list) with `<dt>` / `<dd>` pairs, not a `<table>`

### Screen Reader Support
- Icons that convey meaning have `aria-label` or are accompanied by visible text
- External links have `aria-label` indicating they open in a new tab
- Metric color coding is never the sole means of conveying meaning — values include their sign (`+` / `-`)

---

## Tone and Voice

- **Educational but not condescending** — assume intermediate financial literacy
- **Plain English first** — jargon is always followed by a brief definition on first use
- **Precise** — metrics are never rounded misleadingly; display exactly two decimal places
- **Neutral** — no hype language; let strategy data speak for itself
- **Not financial advice** — all strategy pages carry a brief disclaimer

---

## Design References

- **Primary:** Composer.trade (color language, dark mode, data-forward layout)
- **Secondary:** Wikipedia / wiki-style layout (structured, scannable, cross-linked)
- **Tertiary:** Criterion Collection (editorial credibility, intentional whitespace)
