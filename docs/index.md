---
title: Market Sage — Equity Analysis Archive
hide:
  - toc
---

<div class="ms-hero" markdown="1">

# Market Sage — Equity Analysis Archive

Personal equity research powered by **Market Sage** · Indian Markets  
**Zerodha Kite Portfolio** · Target CAGR 15–18% · Horizon 15–20 Years

</div>

<!-- BEGIN_STATS -->
<div class="ms-stats-strip">
  <div class="ms-stat">
    <span class="ms-stat-label">Total Reports</span>
    <span class="ms-stat-value sv-blue">2</span>
  </div>
  <div class="ms-stat">
    <span class="ms-stat-label">Latest Report</span>
    <span class="ms-stat-value sv-white">9 Jun 2026</span>
  </div>
  <div class="ms-stat">
    <span class="ms-stat-label">Archive Since</span>
    <span class="ms-stat-value sv-white">Jun 2026</span>
  </div>
  <div class="ms-stat">
    <span class="ms-stat-label">Tickers Tracked</span>
    <span class="ms-stat-value sv-amber">21</span>
  </div>
  <div class="ms-stat">
    <span class="ms-stat-label">Report Types</span>
    <span class="ms-stat-value sv-green">2</span>
  </div>
</div>
<!-- END_STATS -->

---

## Reports Archive

<!-- BEGIN_REPORTS_INDEX -->
<span class="ms-year-band">2026</span>

### June 2026

| Date | Report | Category | Tickers | Description |
|------|--------|----------|---------|-------------|
| 9 Jun | [MCX — Multi Commodity Exchange: Comprehensive Equity Analysis](reports/2026/june/mcx-equity-analysis-9jun2026.md) | `analysis` | MCX, BSE, CDSL, +1 more | Full fundamental, technical, governance & forensic analysis of MCX (NSE: MCX). Verdict… |
| 9 Jun | [Market Sage — Indian Stock Action Plan](reports/2026/june/market-sage-action-plan-9jun2026.md) | `action-plan` | COFORGE, NAVINFLUOR, +18 more | Six new positions across BFSI-IT, Specialty Chemicals, Capital Markets, Diagnostics, Ho… |
<!-- END_REPORTS_INDEX -->

---

## HTML Reports

Original analysis reports rendered with the full **Market Sage visual theme** — status badges, phased deployment tables, formatted financial data, and colour-coded action indicators. Identical content to the Markdown reports, presented in standalone HTML.

<a href="https://rrpofficial.github.io/my-equity-analysis-reports/html-reports/" class="md-button md-button--primary" target="_blank" rel="noopener">HTML Reports Archive →</a>

---

## Workflow

**1.** Write your analysis in Markdown → save to `input_reports/report-name-DDmonYYYY.md`

**2.** Add YAML frontmatter at the top of the file:

```yaml
---
title: "Market Sage — Stock Action Plan"
date: 2026-06-09
category: action-plan
tags: [indian-equities, portfolio, zerodha]
description: "Phased deployment plan for ₹5L fresh capital across 7 new positions."
tickers: [COFORGE, NAVINFLUOR, CAMS, LALPATHLAB]
---
```

**3.** Run **`/generate-report-index`** — copies the report to `docs/reports/`, updates this index page, and rebuilds the site nav.

**4.** `git add -A && git commit -m "add report" && git push` — GitHub Actions deploys to `gh-pages` automatically.

---

## Category Reference

| Category | Use For |
|---|---|
| `action-plan` | Phased buy/sell/hold plans with capital allocation per stock |
| `portfolio-review` | Full portfolio P&L review, rebalancing, and thesis checks |
| `sector-analysis` | Deep-dive into a specific sector, theme, or industry |
| `macro` | RBI policy, Union Budget, or macroeconomic impact analysis |
| `watchlist` | Stock watchlist updates and screening results |
| `analysis` | General stock or company fundamental / technical analysis |
