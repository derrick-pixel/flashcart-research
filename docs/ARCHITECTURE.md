# flashcart-research — Architecture & API Reference

> **Note:** This repository is a fork of `competitor-intel-template` (Derrick Teo, MIT 2026).
> It is instantiated for a specific live project: **Flashcart 2026**, an onsite corporate-event
> commerce platform for Singapore. All live Flashcart market data (42 competitors, TAM S$11.4B,
> SAM S$350M, SOM S$25M) is baked into `/template/data/*.json`. The underlying template
> machinery is general-purpose and reusable.

---

## Overview / Purpose

`flashcart-research` is a **static competitive-intelligence workspace** with two audiences:

1. **Derrick (human)** — browses the public showcase site and the admin analytics pages to
   review Flashcart's competitive landscape, pricing strategy, whitespace atlas, and board-grade
   PDF report.

2. **AI agents** — ten named Claude Code subagents dispatch in sequence to populate research
   data, render visualisations, and produce the PDF. The repo encodes the full methodology so
   any future AI session can re-run or refresh the analysis without manual re-briefing.

The site is **entirely static** — no server, no database at runtime. All data lives in JSON
files. The admin analytics layer optionally gates pages behind a Supabase email-OTP overlay
(UI access control only; the data is in the HTML source).

**Live URL:** `https://derrick-pixel.github.io/flashcart-research/` (GitHub Pages, `main /`)

---

## Tech Stack

| Layer | Technology | Version / Notes |
|---|---|---|
| Language | Vanilla HTML5 + ES2020 (ES modules) | No transpile step |
| Charts | Chart.js | `assets/vendor/chart.umd.js` (bundled) |
| PDF export | html2canvas + jsPDF | `assets/vendor/html2canvas.min.js`, `assets/vendor/jspdf.umd.min.js` |
| Auth overlay | Supabase JS SDK v2 | Loaded from jsDelivr CDN; email-OTP only |
| Testing | Node `node:test` (built-in) | Node 20+ required; `node --test` |
| Hosting | GitHub Pages | Static; auto-deploys from `main` |
| Package manager | None / `npm test` only | `package.json` has no dependencies; only a `test` script |
| Node version | 20+ | Required for `node:test` runner |
| CSS | Vanilla CSS custom properties | No preprocessor |
| Fonts (showcase) | Google Fonts | Playfair Display, Source Serif Pro, Inter Tight, JetBrains Mono |
| Fonts (admin zine) | Google Fonts | Archivo Black, Space Mono |

---

## Repository Layout

```
flashcart-research/
│
├── index.html                         # Root redirect → template/admin/index.html
├── package.json                       # Name, version, test script only; no deps
├── .gitignore                         # Ignores node_modules, *.pdf (except showcase/), .gstack/
├── .gitleaksignore                    # Gitleaks suppression (Supabase anon key is public-safe)
├── LICENSE                            # MIT
├── README.md                          # Human + AI quick-start
├── AGENT.md                           # AI-agent briefing: roster, rules, lifecycle
├── METHODS.md                         # Append-only methodology changelog
├── SESSION-PROMPTS.md                 # Verbatim prompt history across all sessions
│
├── methodology/                       # Handbook — one file per agent
│   ├── 00-overview.md                 # Ten-agent workflow diagram + handoff contracts
│   ├── 00-asset-extractor.md          # Agent 0: brand-DNA extraction spec
│   ├── 01-competitor-research-analyst.md
│   ├── 02-market-intelligence-analyst.md
│   ├── 03-pricing-strategy-analyst.md
│   ├── 04-whitespace-blue-ocean-analyst.md
│   ├── 05-website-design-auditor.md
│   ├── 06-data-visualization-engineer.md
│   ├── 07-methodology-curator.md
│   ├── 08-report-generator.md
│   ├── 09-aesthetics-presenter.md
│   ├── FIELD-DICTIONARY.md            # Canonical schema for all JSON fields (§1–§12)
│   └── reference/
│       └── README.md                  # Placeholder for whitespace reference screenshots
│
├── prompts/                           # Literal invocation prompts for each agent
│   ├── invoke-asset-extractor.md
│   ├── invoke-competitor-research-analyst.md
│   ├── invoke-market-intelligence-analyst.md
│   ├── invoke-pricing-strategy-analyst.md
│   ├── invoke-whitespace-blue-ocean-analyst.md
│   ├── invoke-website-design-auditor.md
│   ├── invoke-data-visualization-engineer.md
│   ├── invoke-methodology-curator.md
│   ├── invoke-report-generator.md
│   ├── invoke-aesthetics-presenter.md
│   └── extract-new-patterns.md        # Prompt for default-mode Agent 7 harvest pass
│
├── template/                          # The forkable analytics scaffold (Flashcart-instantiated)
│   ├── index.html                     # Redirect → admin/index.html
│   │
│   ├── admin/                         # Gated analytics pages (Supabase OTP)
│   │   ├── index.html                 # Competitor roster (risograph zine aesthetic)
│   │   ├── insights.html              # Market intelligence + pricing insights
│   │   ├── insights-hosai.html        # Hosai-themed insights variant
│   │   ├── insights-terminal.html     # Terminal-themed insights variant
│   │   ├── insights-zine.html         # Zine-style insights
│   │   ├── insights-zine-palettes.html # Palette explorer for zine
│   │   ├── whitespace.html            # Whitespace atlas (heatmap + attack plans)
│   │   ├── design-audit.html          # Website design audit (pending Agent 5)
│   │   ├── report.html                # PDF report generator (board-grade)
│   │   └── assets/
│   │       └── js/
│   │           └── auth-gate.js       # Supabase email-OTP overlay (self-contained)
│   │
│   ├── data/                          # Live Flashcart research data (JSON)
│   │   ├── competitors.json           # 42 competitors + Top-5 (Agent 1 output)
│   │   ├── market-intelligence.json   # TAM/SAM/SOM + macro signals (Agent 2 output)
│   │   ├── pricing-strategy.json      # Personas, tiers, grants (Agent 3 output)
│   │   ├── whitespace-framework.json  # Strategy canvas + heatmap (Agent 4 output)
│   │   └── brand-tokens.json          # Design tokens (Agent 9 output; presence = styled)
│   │
│   └── assets/
│       ├── css/
│       │   ├── site.css               # Template default variables + base styles
│       │   ├── admin.css              # Admin-page layout + card patterns
│       │   ├── dual-theme.css         # Editorial/SaaS dual-theme (admin pages)
│       │   └── report.css             # PDF page layout (full-bleed A4)
│       ├── js/
│       │   ├── app.js                 # Data loader, sample-data banner, unstyled banner
│       │   ├── dom.js                 # XSS-safe h() / clear() / mount() helpers
│       │   ├── format.js              # fmtSGD, fmtSGDFull, fmtRange, fmtScore, fmtPct
│       │   ├── dual-theme.js          # Theme toggle (editorial ↔ saas) with localStorage
│       │   ├── _tests/
│       │   │   └── dom.test.mjs
│       │   ├── report/
│       │   │   ├── page-templates.js  # Section registry + PDF page renderers
│       │   │   ├── pdf-generator.js   # generatePDF() — html2canvas + jsPDF pipeline
│       │   │   ├── preflight-dom.js   # Phase 4 DOM validator (7 checks)
│       │   │   ├── toc.js             # computePageIndex() — page-number registry
│       │   │   └── _tests/
│       │   │       └── toc.test.mjs
│       │   └── viz/
│       │       ├── radar.js           # Chart.js radar (strategy canvas)
│       │       ├── heatmap.js         # HTML+CSS-grid heatmap + cell-detail panel
│       │       ├── market-funnel.js   # TAM→SAM→SOM derivation flow renderer
│       │       ├── price-bars.js      # Horizontal bar chart (sg_monthly_sgd)
│       │       ├── donut.js           # Target-market distribution donut
│       │       ├── search.js          # Competitor search + filter + debounce
│       │       └── _tests/
│       │           ├── heatmap.test.mjs
│       │           ├── market-funnel.test.mjs
│       │           ├── radar.test.mjs
│       │           └── search.test.mjs
│       └── vendor/
│           ├── chart.umd.js           # Chart.js (bundled, no CDN dependency)
│           ├── html2canvas.min.js     # html2canvas
│           └── jspdf.umd.min.js       # jsPDF
│
└── showcase/                          # Public marketing magazine (dual-theme)
    ├── index.html                     # Magazine landing + workflow diagram
    ├── pricing-thesis.html            # Pricing thesis deep-dive
    ├── pricing-benchmarks.html        # Competitor pricing benchmarks
    ├── buyer-research.html            # Buyer persona research
    ├── assets/
    │   ├── dual-theme.css             # Showcase dual-theme CSS
    │   └── dual-theme.js              # Theme toggle for showcase
    └── exhibits/                      # Prior project case studies
        ├── xinceai.md
        ├── elix-eor.md
        └── lumana.md
```

---

## System Architecture

### Components

```
┌─────────────────────────────────────────────────────────────────┐
│  GitHub Pages (static hosting)                                  │
│                                                                 │
│  ┌──────────────────┐      ┌──────────────────────────────────┐ │
│  │  /showcase/      │      │  /template/                      │ │
│  │  Public magazine │      │  Gated analytics workspace       │ │
│  │                  │      │                                  │ │
│  │  dual-theme.css  │      │  ┌────────────────────────────┐  │ │
│  │  Playfair/Serif  │      │  │  admin/*.html              │  │ │
│  │  Editorial+SaaS  │      │  │  (risograph zine aesthetic) │  │ │
│  │  toggle          │      │  │                            │  │ │
│  │                  │      │  │  ┌──────────────────────┐  │  │ │
│  │  5 public pages  │      │  │  │  auth-gate.js        │  │  │ │
│  └──────────────────┘      │  │  │  Supabase email-OTP  │  │  │ │
│                            │  │  │  (UI gate only)      │  │  │ │
│                            │  │  └──────────────────────┘  │  │ │
│                            │  └────────────────────────────┘  │ │
│                            │                                  │ │
│                            │  ┌────────────────────────────┐  │ │
│                            │  │  data/*.json               │  │ │
│                            │  │  competitors.json          │  │ │
│                            │  │  market-intelligence.json  │  │ │
│                            │  │  pricing-strategy.json     │  │ │
│                            │  │  whitespace-framework.json │  │ │
│                            │  │  brand-tokens.json         │  │ │
│                            │  └───────────┬────────────────┘  │ │
│                            │              │ fetch() at load    │ │
│                            │  ┌───────────▼────────────────┐  │ │
│                            │  │  JS modules (ESM)          │  │ │
│                            │  │  app.js → loadAppData()    │  │ │
│                            │  │  viz/*.js → render*()      │  │ │
│                            │  │  report/*.js → generatePDF │  │ │
│                            │  └────────────────────────────┘  │ │
│                            └──────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
         │
         │ email-OTP auth only
         ▼
┌─────────────────────────┐
│  Supabase               │
│  Project: suehogmzj...  │
│  Auth: email-OTP        │
│  Domain allowlist:      │
│    elitez.asia          │
│    dhc.com.sg           │
│  SMTP via Resend        │
└─────────────────────────┘

  ┌─────────────────────────────────────────────────────────────┐
  │  AI Agent Pipeline (Claude Code subagents — offline)        │
  │                                                             │
  │  [0 Asset Extractor]*                                       │
  │        ↓                                                    │
  │  [1 Competitor] → [2 Market] → [3 Pricing] → [4 Whitespace]│
  │                                         ↘                   │
  │                                    [5 Design]               │
  │                                         ↘                   │
  │                                    [6 Viz] → [8 PDF Report] │
  │                                         ↑                   │
  │                    [7 Methodology Curator — mid-flight]     │
  │                                                             │
  │  [9 Aesthetics Presenter]** → re-skins admin/*.html         │
  │                                                             │
  │  * optional (runs when brand reference materials provided)  │
  │  ** opt-in (runs after human approves unstyled layout)      │
  └─────────────────────────────────────────────────────────────┘
```

### Runtime Data Flow

1. **Page load** — browser fetches `admin/*.html` (static file from GitHub Pages).
2. **Auth check** — `auth-gate.js` calls `supabase.auth.getSession()`. If no session, the full-screen OTP overlay locks the page (`body.ez-gate-locked`). On successful OTP verify, the overlay is removed.
3. **Data load** — each admin page imports `app.js` and calls `loadAppData()`, which fetches all four JSON files in parallel via `Promise.all`. The combined object is written to `window.AppData`.
4. **Banner check** — `mountSampleBanner()` warns if any file has `meta.sample_data: true`. `mountUnstyledBanner()` does a `HEAD` request to `brand-tokens.json`; if absent, emits a banner warning that Agent 9 has not run.
5. **Render** — each admin page's inline `<script type="module">` calls the appropriate `viz/*.js` renderer functions against `window.AppData`, building DOM safely via `h()`.
6. **PDF** — `report.html` has a "Generate PDF" button that calls `generatePDF()`. This runs the Phase 4 preflight DOM validator, then rasterises each `.pdf-page` div with `html2canvas` at scale 2, encodes as JPEG at 0.62 quality, and stitches into an A4 jsPDF document. Target size < 10 MB; sample data produces ~800 KB.

---

## API Reference

### Public Exported JS Functions

All functions are ES module exports. They are consumed by inline `<script type="module">` blocks in the HTML pages. There are no HTTP API endpoints — this is a fully static site.

---

#### `app.js`

| Export | Signature | Description |
|---|---|---|
| `loadAppData` | `async () => { competitors, market, pricing, whitespace }` | Fetches all four data JSONs in parallel. Writes result to `window.AppData`. Throws if any fetch fails. |
| `mountSampleBanner` | `() => void` | Reads `window.AppData`; prepends a sticky warning banner to `document.body` if any file has `meta.sample_data === true`. No-op if AppData absent. |
| `mountUnstyledBanner` | `async () => void` | HEAD-requests `../data/brand-tokens.json`. If absent or network error, prepends an "Un-styled draft" status banner to `document.body`. If present, no-op (Agent 9 has run). |

---

#### `dom.js`

| Export | Signature | Description |
|---|---|---|
| `h` | `(tag: string, props?: object, ...children) => HTMLElement` | XSS-safe DOM element factory. Props: `class` sets `className`; `style` accepts object; `dataset` accepts object; `on*` functions register event listeners; other props set via `el[k]` or `setAttribute`. Children are text-node-escaped. Nested arrays are flattened. `null`/`undefined`/`false` children are skipped. |
| `clear` | `(node: Node) => void` | Removes all child nodes from `node`. |
| `mount` | `(parent: Node, ...children) => void` | Clears `parent`, then appends `children` (same null-handling as `h`). |

---

#### `format.js`

Currency label is read from `window.AppData?.market?.market_size?.currency_label` (defaults to `"SGD"`).

| Export | Signature | Example | Description |
|---|---|---|---|
| `fmtSGD` | `(n: number) => string` | `48000000 → "S$48M"` | Compact: B/M/K suffixes. Uses `currency_label` from AppData. Returns `"—"` for non-finite. |
| `fmtSGDFull` | `(n: number) => string` | `48000000 → "S$48,000,000"` | Full locale-formatted number for tables/appendices. |
| `fmtRange` | `(low, expected, upper: number) => string` | `(199,299,499) → "S$199 — S$499 (target S$299)"` | WTP band display. Returns `"—"` if any arg is non-finite. |
| `fmtScore` | `(n: number, scale: "1-5"\|"0-5"\|"1-10") => string` | `(4.2,"1-10") → "4.2 / 10"` | Explicit scale token required; throws if scale is invalid. |
| `fmtPct` | `(n: number) => string` | `12.5 → "12.5%"` | Strips trailing `.0`. Returns `"—"` for non-finite. |

---

#### `viz/radar.js`

| Export | Signature | Description |
|---|---|---|
| `buildRadarData` | `({ dimensions, scores }) => { labels, datasets }` | Pure: builds Chart.js radar dataset. `"us"` entry always first, with thick border (3px) and fill. Others thin (1.5px), no fill. Missing dimension scores default to 0. |
| `renderRadar` | `({ canvas, dimensions, scores, max? }) => Chart` | Creates and returns a `new Chart` on `canvas`. `max` defaults to 5. Legend on right. |

---

#### `viz/heatmap.js`

| Export | Signature | Description |
|---|---|---|
| `cellCount` | `(cell) => number` | Pure: counts competitors in cell with `score >= 3`. |
| `cellBand` | `(count: number) => "green"\|"amber"\|"red"` | Pure: `<= 1` → green, `2–3` → amber, `>= 4` → red. |
| `buildCellDetail` | `({ segmentId, needId, segmentName, needName, cell }) => object` | Pure: builds click-detail payload with `band`, `verdict`, `ourScore`, sorted competitors list. |
| `renderHeatmap` | `({ container, segments, needs, cells, onCellClick }) => void` | Renders CSS-grid heatmap into `container`. Grid: `240px fixed label column + n × 1fr` for needs. Cell click calls `onCellClick(detail)`. |
| `renderCellDetail` | `(panel, detail) => void` | Renders competitor list with `specialisation_for_cell` into `panel`. |

---

#### `viz/market-funnel.js`

| Export | Signature | Description |
|---|---|---|
| `buildFunnelData` | `(marketSize) => funnelData` | Pure: normalises `market_size` into render shape. Returns `{ stages[], implications[], appendix, sources[], legacy_mode }`. Falls back to legacy-paragraph mode if neither `derivation_flow` nor `implications[]` is present. |
| `renderMarketFunnel` | `(container, marketSize) => void` | Full renderer: funnel flow with stage blocks + arrows, implications grid, collapsed methodology appendix, sources line. Uses `buildFunnelData` internally. |

---

#### `viz/search.js`

| Export | Signature | Description |
|---|---|---|
| `matchesCompetitor` | `(c, query?, filters?) => boolean` | Pure: case-insensitive substring search across `name`, `primary_value_prop`, `hq`, `features[]`, `strengths[]`. Supports `filters.category`, `filters.hqRegion`, `filters.threatLevelMin`. |
| `debounce` | `(fn, ms?) => fn` | 150 ms default debounce. |
| `wireSearch` | `({ input, filtersEl, competitors, onUpdate }) => void` | Wires `input` (text) and `filtersEl` (any container of named `filter-*` inputs) to call `onUpdate(filteredArray)` on change. |

---

#### `viz/price-bars.js`

| Export | Signature | Description |
|---|---|---|
| `renderPriceBars` | `({ canvas, competitors, ourPriceSgd? }) => Chart` | Horizontal bar chart of `sg_monthly_sgd` (up to 20 competitors with non-null prices). If `ourPriceSgd` provided, overlays a dashed orange reference line. |

---

#### `viz/donut.js`

| Export | Signature | Description |
|---|---|---|
| `renderMarketDonut` | `({ canvas, competitors }) => Chart` | Doughnut chart of `target_market` distribution. Aggregates across all competitors; top 8 segments rendered. |

---

#### `report/toc.js`

| Export | Signature | Description |
|---|---|---|
| `computePageIndex` | `(sections, data) => object` | Iterates section registry, calls `section.countPages(data)` for each, accumulates page numbers. Returns `{ [sectionId]: startPage, _total: n }`. |

---

#### `report/page-templates.js`

| Export | Signature | Description |
|---|---|---|
| `buildSections` | `(data) => Section[]` | Returns the ordered section registry: `cover`, `toc`, `exec`, `land` (2 pages), `mkt` (2 pages), `price`, `white` (2 + appendix pages), `des`, `app` (ceil(n/20) pages). |
| `renderPages` | `(root, sections, data) => void` | Clears `root`, computes page index via `computePageIndex`, then calls each section's `render()` method to append `.pdf-page` divs. |

---

#### `report/pdf-generator.js`

| Export | Signature | Description |
|---|---|---|
| `PreflightError` | `class extends Error` | Thrown when Phase 4 pre-flight fails. Has `.violations` array. |
| `generatePDF` | `async ({ root, data, filename, onProgress }) => void` | Runs Phase 4 DOM validation (throws `PreflightError` on failure), then rasterises each `.pdf-page` via `html2canvas` (scale 2, JPEG 0.62), stitches A4 pages in jsPDF, calls `pdf.save(filename)`. `onProgress(current, total)` called after each page. |

---

#### `report/preflight-dom.js`

| Export | Signature | Description |
|---|---|---|
| `validateDOM` | `(root, data) => { ok: boolean, violations: Array<{check, message}> }` | Runs all 7 Phase 4 checks; never short-circuits. Returns all violations in one pass. |
| `formatViolations` | `(violations) => string` | Formats violations as `[Phase 4x] message` lines. |

**Phase 4 checks:**

| Check | Rule |
|---|---|
| 4a | All canonical sections present in order: `cover, toc, exec, landscape, market, pricing, ws-thesis, ws-heatmap, ws-cells, website, appendix` |
| 4b | First page is `data-section="cover"` with class `pdf-cover`; background is not white/transparent |
| 4c | Every non-cover page has `.pdf-footer` with exactly 3 children (project name, page-of-total, date) |
| 4d | TOC page numbers match the actual rendered start page for each of the 7 chapters |
| 4e | Cell Detail Appendix (`ws-cells` pages) has at least one green row and one red row |
| 4f | Appendix has correct page count (`ceil(n/20)`) and exactly `n` table rows |
| 4g | No page (except cover) has fewer than 50 chars of non-footer text |

---

#### `template/admin/assets/js/auth-gate.js`

Self-contained IIFE. No ES module exports. Exposes:

| Global | Type | Description |
|---|---|---|
| `window._sb` | Supabase Client | Signed-in Supabase client; available after session established |
| `window._sbReady` | `Promise<SupabaseClient>` | Resolves when a valid session exists (either pre-existing or after OTP verify) |

Flow: `getSession()` check → if session exists, unlock immediately. Otherwise render email → OTP two-stage overlay. On success, remove overlay, resolve `_sbReady`.

---

## Data Model

### Overview

All state is in five JSON files under `template/data/`. There is no runtime database; pages read these files via `fetch()`. Every file shares the `meta` block shape.

### `meta` block (shared across all data files)

| Field | Type | Description |
|---|---|---|
| `meta.project_name` | string | Human-readable project name (`"Flashcart 2026"`) |
| `meta.brand_tokens.primary` | string | Brand primary hex |
| `meta.brand_tokens.secondary` | string | Brand secondary hex |
| `meta.brand_tokens.accent` | string | Brand accent hex |
| `meta.brand_tokens.neutral_dark` | string | Dark neutral hex |
| `meta.brand_tokens.neutral_light` | string | Light neutral hex |
| `meta.brand_tokens.font_display` | string | Display font family |
| `meta.brand_tokens.font_body` | string | Body font family |
| `meta.research_date` | YYYY-MM-DD | Last full refresh date |
| `meta.sample_data` | boolean | `true` = scaffold dummy data; `false` = real project data |

---

### `competitors.json`

Top-level: `{ meta, top_five[], competitors[] }`

**Current Flashcart state:** 42 competitors, `sample_data: false`, `research_date: 2026-05-01`.

#### `competitors[]` fields

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | yes | Stable FK; lowercase-with-underscores |
| `name` | string | yes | Display name |
| `url` | string | yes | Homepage URL |
| `category` | enum | yes | `global_incumbent` \| `sg_local` \| `regional_challenger` \| `diy_alternative` \| `adjacent` \| `big_si` |
| `hq` | string | yes | City, Country |
| `hq_region` | enum | yes | `SEA` \| `APAC` \| `Global` \| `Other` |
| `target_market` | string[] | yes | Customer segments |
| `countries_covered` | string[] | yes | Countries with paying customers |
| `sg_monthly_sgd` | number\|null | yes | SG price/month; null if not offered or unknown |
| `pricing_range_published` | string | yes | Human-readable price range |
| `pricing_flag` | enum | yes | `public` \| `partial` \| `hidden_estimated` |
| `primary_value_prop` | string | yes | ≤ 120 chars |
| `features` | string[] | yes | Notable features |
| `strengths` | string[] | yes | Evidence-anchored strengths |
| `weaknesses` | string[] | yes | Evidence-anchored weaknesses |
| `threat_level` | integer | yes | 1–5 |
| `beatability` | integer | yes | 1–5 |
| `market_share_estimate_pct` | number\|null | yes | 0–100 or null |
| `research_date` | YYYY-MM-DD | yes | Per-record date |
| `website_design_rating` | number\|null | yes | 1–10 or null (null = Agent 5 not yet run) |
| `website_design_notes` | string | yes | Required if any dimension ≤ 6 |
| `website_screenshot_path` | string | yes | Relative path to screenshot |
| `website_screenshot_path_mobile` | string | no | Mobile 375×667 screenshot path |
| `findability_seconds` | object | no | `{ pricing, demo, contact }` — seconds (0–30 or null) |
| `implications[]` | object[] | no | `{ headline, body, agent_targets[] }` |

#### `top_five[]` fields

| Field | Type | Description |
|---|---|---|
| `competitor_id` | string | FK to `competitors[].id` |
| `rank` | integer | 1–5 |
| `rationale` | string | ≤ 200 chars |

**Flashcart Top-5:** Pico Group (#1), Giftano (#2), OnTheList SG (#3), HoneySpree (#4), DIY HR (#5).

---

### `market-intelligence.json`

Top-level: `{ meta, market_size, policies[], cultural_signals[], economic_signals[], adoption_patterns, trends[] }`

**Flashcart figures:** TAM S$11.4B, SAM S$350M, SOM S$25M.

#### Key sub-schemas

**`market_size.derivation_flow`** — three-stage TAM→SAM→SOM funnel:
- Each stage: `{ stage_label, subtitle, result_label, total_equation, filters[], stacks[] }`
- Each stack: `{ name, source, inputs[{ label, value }], equation, result_label }`

**`market_size.implications[]`** — strategic reads:
- Each: `{ headline (≤ 90 chars), body (≤ 480 chars), agent_targets[] }`

**`policies[]`** — regulatory signals:
- Each: `{ title, body, sentiment (support|neutral|against), effective_date, sunset_date?, url, data_as_of, implication_for_us }`

**`adoption_patterns.country_readiness[]`:**
- Each: `{ country, regulatory (1–5), tech_maturity (1–5), price_tolerance (1–5) }`

---

### `pricing-strategy.json`

Top-level: `{ meta, personas[], pricing_models[], elasticity_heuristics[], recommended_tiers[], grants[] }`

#### `personas[]` (cap: 5; no two may share ≥ 60% of `pains[]`)

| Field | Type | Description |
|---|---|---|
| `id` | string | Stable FK |
| `name` | string | Display name |
| `icp` | string | Ideal customer profile sentence |
| `pains[]` | string[] | Top pains |
| `current_workaround` | string | How they cope today |
| `wtp_band_sgd` | object | `{ low_anchor, expected, upper_stretch }` all numbers |
| `nba` | object | Structured NBA block (see below) |
| `whitespace_segment_ids[]` | string[] | FK to `heatmap.segments[].id` |
| `implications[]` | object[] | `{ headline, body, agent_targets[] }` |

**`personas[].nba` structured block:**

| Field | Type | Notes |
|---|---|---|
| `nba.method` | enum | `wage` \| `competitor_price` \| `tooling_stack` \| `time_value` |
| `nba.summary` | string | ≤ 200 chars |
| `nba.inputs` | object | Fields depend on method |
| `nba.inputs.tooling_lines[]` | object[] | `{ tool, monthly_sgd }` for `tooling_stack` |
| `nba.inputs.competitor_id` | string | FK for `competitor_price` method |
| `nba.inputs.hourly_sgd` | number | For `wage`/`time_value` |
| `nba.inputs.hours_saved_per_month` | number | For `wage`/`time_value` |
| `nba.monthly_sgd_equivalent` | number | Ground-truth figure; `wtp_band_sgd.expected` must be 0.4×–1.2× this |
| `nba.confidence` | number | 0.0–1.0; < 0.6 = exploratory |

#### `recommended_tiers[]`

| Field | Type | Description |
|---|---|---|
| `name` | string | Tier display name |
| `price_sgd` | number | List price |
| `target_persona` | string | Persona name |
| `what_in[]` | string[] | Included items |
| `what_excluded[]` | string[] | Excluded items |
| `psychological_anchor` | string | Anchor framing |
| `effective_price_after_psg` | number | Net price after PSG grant |

#### `grants[]`

| Field | Type | Description |
|---|---|---|
| `name` | string | Grant name (e.g. `"PSG"`) |
| `coverage_pct` | number | 0–100 |
| `cap_sgd` | number\|null | Cap in SGD |
| `eligibility` | string | 1-sentence summary |
| `applies_to_tiers[]` | string[] | Tier names |

---

### `whitespace-framework.json`

Top-level: `{ meta, strategy_canvas, heatmap, attack_plans[] }`

**Flashcart headline thesis:** "Flashcart bridges surplus inventory × HR-channel distribution × PayNow-QR onsite checkout."

#### `strategy_canvas`

| Field | Type | Description |
|---|---|---|
| `headline_thesis` | string | One-sentence whitespace thesis |
| `dimensions[]` | object[] | `{ key, label }` — 8 dimensions for Flashcart |
| `scores` | object | Map `<competitor_id>` → `{ <dim_key>: 0–5 }`. Special key `"us"` = Flashcart's own scores. |

#### `heatmap`

| Field | Type | Description |
|---|---|---|
| `segments[]` | object[] | Row axis: `{ id, name, descriptor }` |
| `needs[]` | object[] | Column axis: `{ id, name, short }` |
| `cells` | object | Map keyed `"<segment_id>:<need_id>"` → `{ our_score (0–5), competitors[] }` |
| `cells[*].competitors[]` | object[] | `{ id (FK), name, score (0–5), specialisation_for_cell (≤ 120 chars, pair-specific) }` |

**Invariant:** `specialisation_for_cell` must be pair-specific to this exact segment-need cell, never generic copy of `strengths`.

#### `attack_plans[]`

| Field | Type | Constraint | Description |
|---|---|---|---|
| `rank` | integer | ≥ 1 | Priority order |
| `niche_name` | string | **≤ 60 chars** | Headline-style niche name |
| `icp` | string | | Ideal customer profile |
| `whitespace_segment_id` | string | FK | Single heatmap row mapping |
| `tam_estimate_sgd` | number | ≥ 0 | Niche TAM |
| `tam_reasoning` | string | | Derivation |
| `why_gap` | string | | Why competitors left this gap |
| `why_we_win` | string | | Our positioning advantage |
| `gtm` | object | | `{ channel, pitch, pricing, content }` |

---

### `brand-tokens.json`

Top-level: `{ meta, extracted_from, tokens, font_imports[], notes }`

Produced by Agent 9. Its **presence in the filesystem** is the signal that admin pages are fully styled (no "un-styled draft" banner). When absent, `mountUnstyledBanner()` in `app.js` emits the warning.

| Field | Type | Description |
|---|---|---|
| `extracted_from.type` | enum | `public_site` \| `brand_assets_json` \| `showcase_dual_theme` \| `manual_override` |
| `extracted_from.reference` | string | URL or file path |
| `extracted_from.captured_at` | YYYY-MM-DD | Token capture date |
| `tokens` | object | All CSS custom property values, including dual-theme tokens |
| `font_imports[]` | string[] | Google Fonts (or other) URLs for `<head>` |
| `notes` | string | Visual idioms and usage notes |

**Flashcart `brand-tokens.json` is present** (dual-theme: editorial cream+oxblood / saas white+purple). Admin pages do not load the Flashcart red/saffron brand overrides — they inherit the template's editorial and SaaS palettes.

---

## Authentication & Authorization

### Mechanism

Supabase email-OTP, delivered via Resend SMTP. All admin pages in `template/admin/` drop in `auth-gate.js` as the last `<script>` before `</body>`.

### Domain allowlist

Enforced by a Supabase **"Before User Created"** Postgres hook on the shared Elitez auth project (`suehogmzjspagcsrqvsw`). Only emails from these domains may create sessions:

- `elitez.asia`
- `dhc.com.sg`

### Session persistence

`localStorage` via Supabase SDK (`persistSession: true`, `autoRefreshToken: true`). Returning users skip the form on page load (`getSession()` check).

### Important limitation

The gate hides the admin UI but does **not** encrypt the page source. Competitor data baked into the HTML is visible in DevTools without authentication. Moving data into Supabase with RLS is a planned future step (noted in `auth-gate.js` comments).

### Showcase pages

Public (`/showcase/*.html`). No auth. `robots: noindex, follow` on admin pages; `noindex, nofollow` on root redirect.

---

## Configuration & Environment Variables

This is a fully static site. There are **no server-side environment variables**. The only credential is the Supabase anon (publishable) key embedded in `auth-gate.js`.

| Name | Location | Purpose |
|---|---|---|
| `SUPABASE_URL` | `auth-gate.js` (hardcoded) | Supabase project URL (public, safe to embed) |
| `SUPABASE_ANON_KEY` | `auth-gate.js` (hardcoded) | Supabase anon/publishable JWT (public, safe to embed) |

Both values are intentionally hardcoded in the client-side IIFE — this is the standard Supabase browser pattern. The `.gitleaksignore` file suppresses false-positive secret-scanner alerts for these values.

**Supabase project reference:** `suehogmzjspagcsrqvsw` (shared with `Elitez-ESOP` and other Elitez properties on the ESOP Supabase project).

---

## Build, Run & Deployment

### There is no build step

The site is plain HTML + vanilla ES modules. Open any `.html` file directly in a browser (with a local server for module imports), or push to GitHub and let Pages serve it.

### Running tests

```bash
cd /Users/derrickteo/codings/flashcart-research
node --test template/assets/js/**/_tests/*.test.mjs
```

Requires Node 20+. Tests use `node:test` (built-in) and a minimal DOM stub (`globalThis.document = ...`) for pure-function tests.

Test files:

| File | What it tests |
|---|---|
| `template/assets/js/_tests/dom.test.mjs` | `h()` element creation, class/attr/event wiring, text-node escaping, null child skipping |
| `template/assets/js/viz/_tests/radar.test.mjs` | `buildRadarData()` ordering, "us" styling, missing-dimension defaults |
| `template/assets/js/viz/_tests/heatmap.test.mjs` | `cellCount()`, `cellBand()`, `buildCellDetail()` |
| `template/assets/js/viz/_tests/market-funnel.test.mjs` | `buildFunnelData()` modern/legacy paths, missing stages |
| `template/assets/js/viz/_tests/search.test.mjs` | `matchesCompetitor()` query/filter logic |
| `template/assets/js/report/_tests/toc.test.mjs` | `computePageIndex()` multi-page sections |

### Local development

```bash
# Serve with any static server, e.g.:
npx serve /Users/derrickteo/codings/flashcart-research
# Then open http://localhost:3000/template/admin/index.html
```

Module `import` statements use relative paths (`../data/`, `../dom.js`) that only work with HTTP, not `file://`.

### Deployment

GitHub Pages, serving from `main /` root. Push to `main` on `derrick-pixel/flashcart-research` (or the configured remote); Pages auto-deploys.

Per `AGENT.md` Rule: **commit after every agent run**, not just at the end.

---

## Notable Implementation Details / Gotchas

### 1. Dual-audience design (human + AI)

`AGENT.md` and `methodology/` are first-class artefacts — they ARE the architecture for the AI agents. A future Claude Code session reading `AGENT.md` has complete context to resume the project without human re-briefing.

### 2. XSS safety via `h()` / no innerHTML

All DOM construction uses `dom.js:h()`, which routes strings through `document.createTextNode`. The codebase has zero HTML-string setters. This is enforced by Agent 7 (mid-flight quality gate) and is the reason competitor names and user-supplied data can be rendered safely.

### 3. Heatmap layout invariants (CSS Grid)

`renderHeatmap()` sets `gridTemplateColumns: 240px repeat(n, 1fr)`. The `1fr` columns use `minmax(0, 1fr)` idiom in `admin.css` to prevent overflow when long competitor names or technical strings are present. `overflow-wrap: anywhere` is applied to all text containers. **Without these two rules, the grid breaks on long strings** — a failure mode discovered during the Elitez Events build (Session 4).

### 4. PDF size budget

Target is < 10 MB (hard limit); `~800 KB` for a 30-competitor sample. Achieved via:
- `html2canvas` scale 2 (not higher)
- JPEG quality 0.62
- jsPDF `compress: true`
- `FAST` image pipeline

Phase 4 check 4g (empty-page detection) catches blank-page leakage — a failure mode seen in the Passage caskets PDF (pages 1-3, 5-7 were blank due to CSS height quirks).

### 5. Un-styled draft banner contract

Every admin page should call `mountUnstyledBanner()` on load. The banner auto-dismisses when `brand-tokens.json` exists (HTTP HEAD request). No code change or localStorage flag is needed to dismiss — it is purely file-presence-driven.

### 6. Inter-agent quality gates (hard halts)

Downstream agents check upstream preconditions before running. Key gates:

| Gate | Condition |
|---|---|
| Agent 1 → 2 | `competitors[].length >= 30` AND `≥ 60%` in `{SEA, APAC}` |
| Agent 3 → 4 | `personas[].length <= 5`, no two share ≥ 60% pains, all `nba.monthly_sgd_equivalent` set |
| Agent 4 → 6 | Every `specialisation_for_cell ≤ 120 chars` and pair-specific; `attack_plans[].niche_name ≤ 60 chars` |
| Agent 6 → 8 | No inline currency/score formatting; `brand-tokens.json` present OR un-styled banner rendered |

### 7. NBA unit consistency gotcha

`wtp_band_sgd.expected` is a **monthly** figure; `nba.monthly_sgd_equivalent` is also monthly. The `× 12` annualisation step must be stated explicitly when comparing against annual figures in grant eligibility (PSG cap is stated annually). This ambiguity is documented as Methodology Improvement Candidate #1 from the Elitez ESOP build (Session 5).

### 8. PDF page template section IDs

The preflight validator (`preflight-dom.js`) asserts section IDs against `CANONICAL_SECTIONS`. These IDs are hardcoded in `page-templates.js:buildSections()` via `dataset: { section: 'cover' }` etc. If a page renderer uses a different `data-section` value than what preflight expects, check 4a will fail. The authoritative list is: `cover, toc, exec, landscape, market, pricing, ws-thesis, ws-heatmap, ws-cells, website, appendix`.

### 9. Showcase vs. admin aesthetic split

The `/showcase/` public site uses the **dual-theme CSS** (editorial cream + oxblood / SaaS white + purple) with Playfair Display and Source Serif Pro. The `/template/admin/` pages use a **risograph zine** aesthetic (newsprint `#d8d4cc`, hot pink `#ff2d8a`, cyan `#00c9d9`, Archivo Black + Space Mono) applied at the Agent 9 styling pass. These are intentionally different: the showcase is the investor-facing magazine; the admin is the internal intelligence workspace.

### 10. `meta.sample_data` flag

Setting this to `true` in any data file triggers the `mountSampleBanner()` yellow sticky banner. Before deploying or sharing the admin link, all four JSON files must have `sample_data: false`. This is enforced as Rule #1 in `AGENT.md`.

### 11. Supabase OTP gate — UI only

As noted in `auth-gate.js`, the OTP gate hides the rendered UI but does not prevent a determined user from reading the page source to see competitor data embedded in the HTML. This is an accepted limitation for a static site; full confidentiality would require server-side rendering or Supabase RLS on a dynamic data layer.

### 12. Flashcart project context

This instance is researching **Flashcart** — onsite flash-sale commerce at Singapore corporate events (D&Ds, town halls, family days). Key findings baked into the current data:
- TAM S$11.4B (SG events + MICE), SAM S$350M (HR-budget events), SOM S$25M (5-year)
- Unique thesis: surplus inventory × HR-channel distribution × PayNow QR checkout
- Primary personas: HR Engagement Lead (MNC) and EX/Culture Manager (mid-size SME)
- Flagship attack plan: brand-funded flash retail as the goodie-bag alternative
