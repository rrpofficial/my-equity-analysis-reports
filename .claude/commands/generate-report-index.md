# /generate-report-index

Scan `input_reports/` for `.md` files, copy each report into the dated docs directory structure, update `docs/index.md` with the live report index, and update `mkdocs.yml` nav — all in one idempotent pass.

---

## Step 1 — Discover source reports

Run: `find input_reports -name "*.md" -type f | sort`

If no `.md` files are found, print:
> No markdown reports found in `input_reports/`. Add `.md` files with YAML frontmatter and re-run.

Then stop.

---

## Step 2 — Parse each report

For each `.md` file, read the content and extract:

**From YAML frontmatter** (lines between the opening and closing `---` delimiters at the very top of the file):
- `title` — string
- `date` — YYYY-MM-DD
- `category` — one of: `action-plan`, `portfolio-review`, `sector-analysis`, `macro`, `watchlist`, `analysis`
- `tags` — list of strings (may be absent)
- `description` — string (one-line summary, max 160 chars)
- `tickers` — list of ticker symbols (may be absent)

**Fallback rules when frontmatter is absent or a field is missing:**

| Field | Fallback |
|-------|----------|
| `date` | Parse from filename: `9jun2026` → 2026-06-09; `09-jun-2026` → same; `2026-06-09` → as-is; `jun2026` → 2026-06-01. If unparseable, skip the file and **warn** the user. |
| `title` | Strip date suffix/prefix and `.md` from the filename, replace hyphens and underscores with spaces, apply title case. |
| `category` | Default to `analysis`. |
| `description` | Use the first paragraph of body text that is NOT a heading, blockquote, or empty. Strip markdown syntax. Truncate at 160 chars. |
| `tickers` | Scan body text for sequences of 3–10 uppercase letters/digits (e.g. `COFORGE`, `NAVINFLUOR`). Return unique matches up to 20. |

Build an in-memory list of report objects:
```
{ title, date (YYYY-MM-DD), category, tags, description, tickers, src_path, year (int), month_num (int 1-12), month_name (e.g. "june"), month_label (e.g. "June 2026"), dest_filename, dest_path }
```

---

## Step 3 — Copy reports to docs structure

For each report:
- Destination directory: `docs/reports/<year>/<month_name>/`  
  Example: `docs/reports/2026/june/`
- Destination path: same filename as source, e.g. `docs/reports/2026/june/market-sage-action-plan-9jun2026.md`
- Create the destination directory if it does not exist.
- **If a file already exists at the destination with identical byte-for-byte content, skip the copy** (idempotent).
- **If the source file has no YAML frontmatter**, prepend the inferred frontmatter to the copied file. Do NOT modify the source file in `input_reports/`. The copy in `docs/reports/` gets the generated frontmatter.

Frontmatter to prepend to the copy:
```yaml
---
title: "<inferred title>"
date: <YYYY-MM-DD>
category: <category>
tags: [<tag1>, <tag2>]
description: "<description>"
tickers: [<TICKER1>, <TICKER2>]
---

```

---

## Step 4 — Update docs/index.md

Read the current `docs/index.md`. It contains two pairs of HTML comment markers:

1. `<!-- BEGIN_STATS -->` … `<!-- END_STATS -->` — replace the content between with a fresh stats strip
2. `<!-- BEGIN_REPORTS_INDEX -->` … `<!-- END_REPORTS_INDEX -->` — replace the content between with the year/month grouped report index

**Do NOT modify anything outside the markers.** Replace only the text between each pair of markers (exclusive — keep the marker lines themselves).

### Stats strip content to inject (between BEGIN_STATS / END_STATS)

Compute:
- `total` — count of all reports
- `latest_label` — date of newest report formatted as "9 Jun 2026"
- `since_label` — month+year of oldest report, e.g. "Jun 2026"
- `ticker_count` — count of unique tickers across all reports
- `category_count` — count of distinct category values

Generate:
```html
<div class="ms-stats-strip">
  <div class="ms-stat">
    <span class="ms-stat-label">Total Reports</span>
    <span class="ms-stat-value sv-blue">{total}</span>
  </div>
  <div class="ms-stat">
    <span class="ms-stat-label">Latest Report</span>
    <span class="ms-stat-value sv-white">{latest_label}</span>
  </div>
  <div class="ms-stat">
    <span class="ms-stat-label">Archive Since</span>
    <span class="ms-stat-value sv-white">{since_label}</span>
  </div>
  <div class="ms-stat">
    <span class="ms-stat-label">Tickers Tracked</span>
    <span class="ms-stat-value sv-amber">{ticker_count}</span>
  </div>
  <div class="ms-stat">
    <span class="ms-stat-label">Report Types</span>
    <span class="ms-stat-value sv-green">{category_count}</span>
  </div>
</div>
```

### Reports index content to inject (between BEGIN_REPORTS_INDEX / END_REPORTS_INDEX)

Group reports by year (descending), then by month (descending within year).

For each year, output a `<span class="ms-year-band">` header.

For each month within that year, output a `### Month YYYY` heading followed by a Markdown table:

```markdown
<span class="ms-year-band">2026</span>

### June 2026

| Date | Report | Category | Tickers | Description |
|------|--------|----------|---------|-------------|
| 9 Jun | [Market Sage — Stock Action Plan](reports/2026/june/market-sage-action-plan-9jun2026.md) | `action-plan` | COFORGE, NAVINFLUOR, +4 more | Phased deployment plan for ₹5L... |
```

**Table formatting rules:**
- **Date**: `D Mon` format (e.g. `9 Jun`, `14 Feb`)
- **Report**: linked title using the `dest_path` relative to `docs/` (so `reports/2026/june/...`)
- **Category**: use inline code backticks
- **Tickers**: comma-separated, first 3 tickers; if more than 3 append `, +N more`; if empty put `—`
- **Description**: truncated at 80 chars with `…` if longer

---

## Step 5 — Update mkdocs.yml nav

Read `mkdocs.yml`. It contains two comment markers on their own lines:
```
  # BEGIN_REPORTS_NAV
  # END_REPORTS_NAV
```

Replace everything **between** (exclusive) these two lines with a Reports nav subtree built from the processed reports.

**Format** (maintain 2-space indentation per level):
```yaml
  - Reports:
    - "2026":
      - "June 2026":
        - reports/2026/june/market-sage-action-plan-9jun2026.md
```

Rules:
- Years are sorted descending (newest first)
- Months within a year are sorted descending (newest first)
- Report files within a month are sorted descending by date
- Year and month labels are quoted strings to avoid YAML parsing issues
- If there is only one report under a month, still nest it under the month label
- Do NOT change any other part of `mkdocs.yml`

---

## Step 6 — Report to user

After completing all steps, print a summary:

```
✓ Processed N report(s)
✓ Copied to docs/reports/ (M new, K skipped identical)
✓ Updated docs/index.md (stats + report index)
✓ Updated mkdocs.yml nav

Reports indexed:
  • 9 Jun 2026 — Market Sage — Stock Action Plan [action-plan]
  ...

Next: git add -A && git commit -m "add reports" && git push
```

If any report was skipped due to an unparseable date, list it under `⚠ Skipped:`.

---

## Notes

- This command is safe to re-run at any time — it is fully idempotent.
- Source files in `input_reports/` are **never modified**; only copies in `docs/reports/` are created/updated.
- HTML files (`.html`) in `input_reports/` are silently ignored.
- The `docs/index.md` and `mkdocs.yml` are surgically updated — only the content between the markers changes.
