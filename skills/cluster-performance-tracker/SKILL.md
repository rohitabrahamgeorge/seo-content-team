---
name: cluster-performance-tracker
description: Tracks the performance of published cluster posts on a 15-day cadence. Reads live blog URLs to confirm published metadata (title, slug, primary keyword, publish date), ingests GSC data from a manually-exported CSV, updates the cluster Google Sheet (the single source of truth), and generates a 15-day monthly report. Use this skill on the recurring 15-day cadence after a cluster is published, whenever the blog manager runs "the performance check," when GSC data has been freshly downloaded and dropped into the input folder, or when someone asks "how is the [cluster] cluster performing." Trigger on phrases like "run the 15-day check", "update the cluster Google Sheet", "pull GSC data", "performance report for [cluster]", "monthly cluster report", or any handoff from the blog manager that asks for the cadence-based performance update. This skill does not edit content. It tracks what's already published.
---

# Cluster Performance Tracker

This is the 15-day cadence skill. It does not write content, generate visuals, or recommend changes. Its only job is to capture what's actually happening to published cluster posts and update the single source of truth (the cluster Google Sheet).

## When to use this skill

Trigger when:
- It's been 15 days since the last performance check (or the cluster just published, in which case start the cadence)
- The blog manager has freshly exported a GSC CSV and dropped it into the cluster's `gsc-imports/` folder
- Someone asks "how is the cluster performing"
- A monthly cluster review is happening
- The performance-optimizer skill needs fresh data

Required inputs:
- Cluster name and folder path
- A GSC CSV export covering the cluster URLs and the date range since the last check (manual export from search.google.com/search-console)
- Edit access to the cluster Google Sheet (Sheets API credentials configured for the orchestrator workspace)
- The cluster's `cluster-state.json` to know which posts are actually published

If the GSC CSV is missing, halt and ask the blog manager to export and drop it. Do not invent data.

## Why CSV instead of GSC API (for now)

The first-version setup uses manual CSV exports because it ships immediately without IT setup. The blog manager exports from GSC every 15 days and saves the file to `clusters/[cluster-name]/gsc-imports/[YYYY-MM-DD].csv`. The skill processes whatever CSV is freshest.

When the team adds a service-account-based GSC API integration later, the same skill's read function gets swapped from `read_gsc_csv()` to `fetch_gsc_api()`. Everything downstream stays the same.

## What the skill does, step by step

### Step 1: Identify the cluster scope

Read `cluster-state.json` and pull the list of posts marked `status: "published"`. The skill only tracks live posts. Drafts and unpublished posts are skipped.

### Step 2: Verify each published post is live

For each published slug, fetch the live URL:

```python
BLOG_URL = "{{WEBSITE}}/blog"  # Blog URL from product-context.md
url = f"{BLOG_URL}/{slug}"
response = requests.get(url, timeout=10)
if response.status_code != 200:
    flag_as_unreachable(slug, response.status_code)
    continue
```

If a post returns 404 or 5xx, flag it in the report. The blog manager investigates whether the post was unpublished, the slug was changed, or the CMS is misbehaving.

### Step 3: Extract live metadata from each post

Parse the live HTML for:
- `<h1>` text → confirms the title matches the frontmatter declared title
- `<link rel="canonical">` → confirms the canonical URL
- `<meta property="article:published_time">` → confirms publish date
- The first `<meta name="description">` → confirms meta description renders
- Schema JSON-LD blocks → confirms schema is rendering server-side
- Article body word count → useful for performance correlation

If any extracted value differs from the cluster Google Sheet, flag the drift. Typical drift cases: someone edited the post title in CMS without updating the sheet, or the publish date got rewritten by the CMS on a save action.

### Step 4: Ingest the GSC CSV

GSC CSV export format (from search.google.com/search-console, "Performance" report, "Pages" tab, "Export" → "Download CSV"):

```csv
Top pages,Clicks,Impressions,CTR,Position
{{WEBSITE}}/blog/{{PRODUCT_SLUG}}-vs-globex,142,4823,2.94%,8.3
{{WEBSITE}}/blog/best-globex-alternatives,98,3201,3.06%,9.1
...
```

Or for the "Queries" tab:

```csv
Top queries,Clicks,Impressions,CTR,Position
{{PRODUCT}} vs globex,87,1923,4.52%,6.2
globex alternatives,45,2104,2.14%,11.4
...
```

The skill reads both views (Pages and Queries) if both CSVs are present. The `gsc-imports/` folder convention:

```
gsc-imports/
├── 2026-04-25-pages.csv
├── 2026-04-25-queries.csv
├── 2026-05-10-pages.csv
└── 2026-05-10-queries.csv
```

The skill picks the freshest pair and joins them on URL ↔ query.

### Step 5: Compute deltas vs. previous cycle

For each post URL, compare the current cycle's data to the previous cycle's data (already in the Google Sheet):

```python
delta_clicks = current_clicks - previous_clicks
delta_impressions = current_impressions - previous_impressions
delta_ctr = current_ctr - previous_ctr
delta_position = previous_position - current_position  # Lower position is better, so flip sign
```

Flag thresholds:
- **Significant drop:** Δclicks < -30% OR Δposition > +5 (rank degraded by 5+ spots)
- **Significant gain:** Δclicks > +30% OR Δposition < -5 (rank improved by 5+ spots)
- **Plateau:** clicks stable but impressions growing → check if a CTR optimization is needed

### Step 6: Update the cluster Google Sheet

The cluster Google Sheet has these columns (in order):

| Column | Source | Update cadence |
|---|---|---|
| post_title | live HTML or frontmatter | per cycle |
| slug | URL | per cycle |
| post_url | constructed | per cycle |
| primary_keyword | cluster plan | per cycle (rarely changes) |
| funnel_stage | cluster plan | per cycle (rarely changes) |
| publish_date | live HTML | per cycle (snapshot once, then static) |
| word_count | live HTML | per cycle |
| current_clicks | GSC CSV | per cycle |
| current_impressions | GSC CSV | per cycle |
| current_ctr | GSC CSV | per cycle |
| current_position | GSC CSV | per cycle |
| previous_clicks | last cycle's current_clicks | per cycle |
| previous_position | last cycle's current_position | per cycle |
| delta_clicks | computed | per cycle |
| delta_position | computed | per cycle |
| flag | computed (drop/gain/plateau/normal) | per cycle |
| top_query_1 | GSC queries CSV | per cycle |
| top_query_2 | GSC queries CSV | per cycle |
| top_query_3 | GSC queries CSV | per cycle |
| schema_renders_ok | live HTML check | per cycle |
| canonical_correct | live HTML check | per cycle |
| last_checked | today's date | per cycle |

Write into the sheet via the Sheets API:

```python
from googleapiclient.discovery import build
from google.oauth2 import service_account

SCOPES = ['https://www.googleapis.com/auth/spreadsheets']
SHEET_ID = os.getenv('CLUSTER_SHEET_ID')

creds = service_account.Credentials.from_service_account_file(
    os.getenv('SHEETS_SA_KEY_PATH'),
    scopes=SCOPES
)
service = build('sheets', 'v4', credentials=creds)

# Read existing rows to preserve history columns
existing = service.spreadsheets().values().get(
    spreadsheetId=SHEET_ID,
    range=f'{cluster_name}!A:V'
).execute().get('values', [])

# Build the updated row set
updated_rows = build_rows(cluster_state, gsc_data, existing)

# Write back (replace, don't append, to keep one row per slug)
service.spreadsheets().values().update(
    spreadsheetId=SHEET_ID,
    range=f'{cluster_name}!A:V',
    valueInputOption='RAW',
    body={'values': updated_rows}
).execute()
```

If the sheet doesn't have a tab for this cluster yet, create it. One tab per cluster. The first row is always the header.

### Step 7: Generate the 15-day report

Output a single markdown file at `clusters/[cluster-name]/monthly-reports/[YYYY-MM-DD].md`:

```markdown
# Performance Report: [cluster name]

**Cycle:** [N] of N
**Date:** [today]
**Period covered:** [previous cycle date] to [today]
**Posts tracked:** [count]

## Summary

- Total clicks (period): [N] ([Δ] vs. previous cycle)
- Total impressions (period): [N] ([Δ])
- Average CTR: [%] ([Δ])
- Average position: [N] ([Δ])
- Posts with significant gain: [count]
- Posts with significant drop: [count]
- Posts on plateau: [count]
- Posts unreachable (404/5xx): [count]

## Posts with significant gain

[Per-post: slug, current_position, delta, top contributing query]

## Posts with significant drop

[Per-post: slug, current_position, delta, top declining query, recommendation flag for the optimizer]

## Posts on plateau

[Per-post: slug, impressions trending up but clicks flat, suggested CTR optimization area]

## Schema and metadata drift detected

[Per-post: slug, what drifted, suggested fix for the blog manager]

## Top queries this cycle (cluster-wide)

[Top 20 queries with clicks, impressions, position, post URL receiving the click]

## Queries we're not yet ranking for

[Queries pulled from GSC where impressions exist but position > 20 — these are AEO opportunities for the optimizer]

## Recommended actions

- For the blog manager: [list of CMS-level fixes from drift detection]
- For the performance-optimizer skill: [list of posts to feed into the next optimization pass]
- For the cluster-writer: [list of posts that need full content refreshes, if any]

## Cycle metadata

- GSC CSV processed: [filename]
- Sheet rows updated: [count]
- Next cycle: [today + 15 days]
```

### Step 8: Update cluster-state.json

Append to the state file:

```json
{
  ...existing state...
  "performance_tracking": {
    "last_check_date": "2026-04-25",
    "next_check_date": "2026-05-10",
    "cycles_completed": 1,
    "latest_report_path": "monthly-reports/2026-04-25.md",
    "sheet_url": "https://docs.google.com/spreadsheets/d/[ID]/edit#gid=[TAB_GID]"
  }
}
```

## What this skill does NOT do

- Does not write or rewrite posts
- Does not edit the live blog
- Does not interpret GSC data beyond computing deltas (interpretation is the optimizer's job)
- Does not pull GSC API data (manual CSV only, for v1)
- Does not edit the cluster plan or state machine
- Does not run on unpublished or drafted posts

## CSV input format requirements

The GSC CSV must come from search.google.com/search-console:

1. Go to GSC → "Performance on Search results"
2. Set the date range to "Last 28 days" (or the period since the last cycle)
3. Filter by the cluster's URL prefix (e.g. `{{WEBSITE}}/blog/{{PRODUCT_SLUG}}-vs-{competitor}`)
4. Click the "Pages" tab → Export → "Download CSV" → save as `gsc-imports/[YYYY-MM-DD]-pages.csv`
5. Click the "Queries" tab → Export → "Download CSV" → save as `gsc-imports/[YYYY-MM-DD]-queries.csv`

Both files must be present. If only one is, halt and ask the blog manager to export the other.

## Failure modes and recovery

### CSV column headers don't match expected format
GSC sometimes changes column names ("CTR" → "Average CTR"). The skill should check column names and fail loudly with a list of expected vs. found headers. The blog manager re-exports.

### Post is published but has zero GSC data
Normal for the first 28 days after publish. Mark as `flag: "indexing"` and don't compute deltas. After 28 days, if still zero impressions, flag as `flag: "not_indexed"` and recommend the blog manager check for indexability issues (robots.txt, noindex meta, canonical pointing elsewhere, etc.).

### Sheet API quota exceeded
Sheets API has a 500-write-per-100-seconds quota per project. The skill should batch writes (one batchUpdate call per cycle) rather than one call per row. If quota is hit, retry with exponential backoff.

### Slug changed after publish
If the live HTML's canonical URL doesn't match the slug declared in cluster-state.json, the skill flags this as drift. The blog manager either updates the cluster state to reflect the new slug, or restores the original slug in CMS.

### Multiple GSC exports for the same date
The skill picks the most recent file by mtime. The blog manager can manually delete stale exports.

## Output artifacts per cycle

After a successful run, these files exist:

```
clusters/[cluster-name]/
├── monthly-reports/
│   └── [YYYY-MM-DD].md          # this cycle's report
├── gsc-imports/
│   ├── [YYYY-MM-DD]-pages.csv   # the CSV that was processed
│   └── [YYYY-MM-DD]-queries.csv
└── cluster-state.json            # updated with performance_tracking section
```

And the live cluster Google Sheet has the new row data.

## Sample call from the orchestrator

```bash
cd clusters/{{PRODUCT_SLUG}}-vs-{competitor}
# Blog manager has just dropped GSC CSVs:
ls gsc-imports/
# 2026-04-25-pages.csv  2026-04-25-queries.csv

# Trigger the performance tracker
python -m performance_tracker.run \
  --cluster {{PRODUCT_SLUG}}-vs-{competitor} \
  --gsc-pages gsc-imports/2026-04-25-pages.csv \
  --gsc-queries gsc-imports/2026-04-25-queries.csv \
  --sheet-id $CLUSTER_SHEET_ID
```

The skill returns the path to the new monthly report. Blog manager opens it and queues follow-ups.

## Next-skill handoff

After the report exists, the orchestrator can optionally trigger the **cluster-performance-optimizer** skill (skill 7), passing the report path. The optimizer reads the report, joins it with the original post content, and produces specific change recommendations.

The performance tracker does not auto-trigger the optimizer. Blog manager judgment decides when to optimize (typically: when 2+ posts are flagged as "drop" in a single cycle, or when the cluster has had 3+ stable cycles and the team wants to push gains further).
