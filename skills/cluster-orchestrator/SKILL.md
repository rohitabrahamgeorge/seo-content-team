---
name: cluster-orchestrator
description: Master orchestrator for building complete SEO content clusters for competitor comparisons of the product described in product-context.md. Use this skill whenever the user asks to build a content cluster, run the cluster system, create a "[Product] vs [Competitor]" content set, build a comparison cluster, run the hub-and-spoke playbook, or starts a request with "{{PRODUCT}} vs", "[Product] vs [Competitor]", "build cluster for", "compete with", or "[Competitor] alternatives cluster". This skill reads the cluster name from the user, confirms scope, and then orchestrates the full chain of cluster skills (planner, writer, quality-checker, visual-designer, link-validator, performance-tracker, performance-optimizer) by telling Claude which skill to invoke at each stage. The blog-manager-sop is a static reference doc, not invoked. Trigger this skill aggressively for ANYTHING related to building a competitor comparison content cluster. This is the master skill, so do NOT invoke individual cluster skills directly when the user asks for a full cluster build.
---

# Cluster Orchestrator

## Overview

This is the master orchestrator for a hub-and-spoke competitor-comparison content system. It is modeled on PostHog's public Mixpanel comparison cluster (14 URLs, 1 pillar, 6 spoke types, all bidirectionally linked) which has been reverse-engineered into a reusable system.

**Your job as the orchestrator is NOT to write content yourself.** Your job is to:
1. Confirm `product-context.md` exists and is filled (see Step 0)
2. Capture the cluster scope from the user
3. Set up the working folder and persistence
4. Invoke each downstream skill in the correct order
5. Track state across runs so the system is resumable
6. Deliver the final cluster as a coherent package

The downstream skills are:
- `cluster-planner`: does SERP, PAA, and competitor analysis; outputs the cluster plan
- `cluster-writer`: writes one post at a time using PostHog templates
- `cluster-visual-designer`: generates 2 visual assets per post
- `cluster-quality-checker`: audits each post against a 25-point checklist
- `cluster-link-validator`: verifies the link topology is bidirectional
- `cluster-performance-tracker`: runs every 15 days against GSC + GA + Bing AI Performance

## Workflow

### Step 0: Load product context

Every skill in the chain reads `product-context.md`, the filled file at the root of the working folder (next to `clusters/`). It is the single source of truth for product name, first-mention rule, website, docs URL, product areas, proof points, migration help offered, voice and competitor set.

If `product-context.md` is missing, copy the blank template at `cluster-planner/references/product-context-template.md` to the root of the working folder and fill it in with the user in one short intake before any planning. Do not proceed to Step 1 until the file exists and the Brand basics, Verified capabilities, Voice and Competitor set sections are filled.

### Step 1: Capture cluster scope

When triggered, parse the user's request to extract:
- **Competitor name** (e.g. "Globex", or any name from the Competitor set in product-context.md)
- **Cluster type**: is this a head-to-head ("{{PRODUCT}} vs Globex") or an alternatives play ("Globex alternatives")?
- **Vertical context** if mentioned (e.g. "for agencies", "for B2B enterprises")

If any of these are unclear, ask the user ONCE with a single multi-option question. Do not interrogate.

Then confirm scope by stating back:
> "Building the {{PRODUCT}} vs {Competitor} cluster. This will produce ~10 interlinked pieces of content following the PostHog Mixpanel template: 1 pillar comparison post, 1 alternatives listicle, 1 migration guide, 3 customer story stubs, 1 narrative post, plus services/pricing references. OK to proceed?"

If product-context.md lists no customer stories, say the 3 customer story spokes are deferred (placeholders only) rather than planned.

Wait for confirmation. If the user wants a smaller scope (e.g. "just the pillar for now"), respect it and update the plan accordingly.

### Step 2: Confirm persistence location

Ask the user where cluster artifacts should persist:
- Google Drive (default, recommended; uses the connected Google Drive MCP)
- Local working directory only (one-shot, not recommended for long-term)
- GitHub (if they have a content repo set up)

This decision matters because the performance monitor (run 15 days later) needs to find the cluster tracker. Default to Google Drive unless user specifies otherwise.

### Step 3: Initialize working folder and state file

Create the folder `clusters/{{PRODUCT_SLUG}}-vs-{competitor-slug}/` at the chosen persistence location. Inside, create:

```
clusters/{{PRODUCT_SLUG}}-vs-{competitor-slug}/
├── README.md                    (overview + index of all artifacts)
├── cluster-state.json           (resumable state — see schema below)
├── cluster-plan.md              (output of planner)
├── cluster-tracker.tsv          (Google Sheets-compatible master tracker)
├── serp-analysis.md             (output of planner)
├── posts/                       (one .md file per post)
├── images/                      (2 SVG assets per post)
├── qa-reports/                  (one report per post)
└── monthly-reports/             (output of performance monitor over time)
```

**cluster-state.json schema:**
```json
{
  "cluster_name": "acme-vs-globex",
  "competitor": "Globex",
  "created_at": "2026-04-25",
  "persistence": "google-drive",
  "scope": {
    "pillar": "planned",
    "alternatives_listicle": "planned",
    "migration_guide": "planned",
    "narrative_post": "planned",
    "customer_stories": "deferred"
  },
  "stages": {
    "planning": "pending",
    "writing": "pending",
    "visuals": "pending",
    "qa": "pending",
    "link_validation": "pending"
  },
  "posts": [],
  "last_updated": "2026-04-25",
  "next_performance_check": "2026-05-10"
}
```

Update `last_updated` and `stages` after every successful step.

### Step 4: Invoke the planner

Tell Claude:
> "Now invoking the cluster-planner skill. This will do SERP analysis on parallel competitor comparisons (since '{{PRODUCT}} vs {X}' may have no organic traffic yet), pull People Also Ask, identify content gaps, and output the full cluster plan."

The planner skill will:
- Run web searches on parallel competitor pairs (e.g. for a Globex cluster: "Globex vs Initech", "Globex vs Umbrella", "Globex vs Hooli")
- Pull SERP features and PAA
- Reference the product's help center or knowledge base ({{DOCS_URL}}, from product-context.md) for tutorial/utility content
- Output `cluster-plan.md`, `cluster-tracker.tsv`, `serp-analysis.md`

If GSC is not connected, the planner will halt and output `keyword-list-for-gsc.txt` for the user to manually pull keyword data from Google Search Console screenshots. **The orchestrator must surface this to the user clearly** and wait for them to paste GSC data back before resuming.

After planner completes, update `cluster-state.json`: `stages.planning = "complete"` and populate the `posts` array with planned slugs.

### Step 5: Loop the writer

For each planned post in the plan, invoke `cluster-writer` ONE AT A TIME, in this order (PostHog priority order):

1. Pillar comparison post (highest leverage, write first)
2. Alternatives listicle (catches MOFU traffic)
3. Migration guide (BOFU conversion)
4. Narrative post (TOFU magnet, optional)
5. Any additional spokes from the plan

After each post, update `cluster-state.json` with that post's slug under `posts[]` and set `posts[i].status = "written"`.

**Do not batch-write all posts in one shot.** Each post deserves full context attention, and writing them sequentially lets the user catch voice drift early.

### Step 6: Generate visuals per post

After each post is written, invoke `cluster-visual-designer` for that post. The designer will produce 2 SVG visuals chosen contextually based on post type (comparison infographic + decision visual for pillar; data flow diagram for migration guide; persona cards for alternatives listicle).

Update `posts[i].visuals = ["path1.svg", "path2.svg"]` in state.

### Step 7: QA each post

After each post has its visuals, invoke `cluster-quality-checker`. If QA fails, the QA skill will return a list of specific fixes; pass those back to `cluster-writer` for a targeted patch (not a full rewrite). Re-run QA. Repeat until pass.

Update `posts[i].qa_status = "pass"` when complete.

### Step 8: Link validation

Once all posts are written, visualed, and QA'd, invoke `cluster-link-validator`. This walks the entire cluster and:
- Verifies every spoke links back to the pillar (with descriptive anchor text)
- Verifies the pillar links to every spoke
- Detects orphan posts and dead links
- Generates the final SVG topology diagram showing actual link structure

Update `stages.link_validation = "complete"` and write the topology SVG into the cluster folder.

### Step 9: Schedule performance monitoring

Set `cluster-state.json.next_performance_check` to today + 15 days. Add a Google Calendar reminder (use the connected Google Calendar MCP) titled "Cluster performance check: {{PRODUCT_SLUG}}-vs-{competitor}" for that date. Reminder description should be:

> "Run cluster-performance-tracker on /clusters/{{PRODUCT_SLUG}}-vs-{competitor}/. Pull GSC, GA, Bing AI Performance data and generate the 15-day report."

### Step 10: Deliver the cluster summary

Generate the final `README.md` in the cluster folder containing:
- One-paragraph cluster summary
- Hub-and-spoke topology diagram (the SVG from step 8)
- Table of all posts with status, slug, primary keyword, internal link count
- Link to the performance monitor schedule
- "What to do next": 3 concrete actions (publish to CMS, share with team, schedule promotion)

Present this README to the user with `present_files` so they have a single entry point to the cluster.

## State recovery (resuming a paused cluster)

If the user invokes this orchestrator on an existing cluster (e.g. they paused mid-build to provide GSC data, or QA failed and needs another pass):

1. Read `cluster-state.json` from the persistence location
2. Identify the last completed stage from `stages`
3. Resume from the next pending stage
4. Do NOT redo completed work

If state file is corrupt or missing, ask the user whether to start fresh or load from the last-known artifact files.

## Critical rules

- **Never write content yourself.** Always invoke `cluster-writer`. The orchestrator is a conductor, not a performer.
- **Never skip QA.** Every post must pass QA before moving on. Quality compounds across the cluster.
- **Always update cluster-state.json after each step.** This is the single source of truth for resumability.
- **Always confirm before destructive actions.** Overwriting an existing cluster, deleting posts, or changing scope mid-build all require user confirmation.
- **Respect the voice rules in product-context.md.** The writer skill enforces these (no em dashes, no ampersands by default, and the voice of the voice owner named in product-context.md, read from the brand voice skill named in product-context.md, if one is installed, otherwise the Voice section of product-context.md), but if you're tempted to draft content yourself in any step, STOP and invoke the writer.
- **The product knowledge base is for tutorials/utilities only.** Reference {{DOCS_URL}} when the planner suggests a migration guide or utility post needs supporting docs. The KB is NOT a source for blog post content.

## What NOT to do

- Do NOT write the pillar post yourself even if it would be faster. Always delegate to the writer.
- Do NOT skip the planner even if you "already know" what posts to write. The planner does live SERP analysis that's irreplaceable.
- Do NOT generate stock images or generic visuals. The visual-designer skill produces custom SVGs with text and data.
- Do NOT proceed without persistence confirmation. A cluster that lives only in a Claude session breaks the self-improving loop.
- Do NOT trigger this skill for one-off blog posts. Use `seo-blog-writer` for single posts; this orchestrator is for full clusters only.

## Reference: PostHog cluster template

The full PostHog vs Mixpanel hub-and-spoke teardown is the foundational reference for this system. Key patterns the planner and writer follow:

**Per-cluster artifacts (PostHog uses 14 URLs):**
1. Pillar comparison (`/blog/{us}-vs-{them}`): ~5,000 words, 9 feature tables, 3 pricing tables, 20-question FAQ
2. Alternatives listicle (`/blog/best-{them}-alternatives`): ~3,500 words, 4 main entries + 5 honorable mentions, 14-question FAQ
3. Migration tutorial (`/docs/migrate/{them}` or `/tutorials/{them}-to-{{PRODUCT_SLUG}}`): ~1,200 words, technical second-person imperative
4. Customer stories (`/customers/{brand}`): ~500-700 words each, quote-led, two pull-quotes (DEFERRED if product-context.md lists no customer stories)
5. Narrative testimonial (`/blog/why-i-{verb}-{them}-for-{{PRODUCT_SLUG}}`): ~900 words, first-person, guest-author preferred
6. Services/concierge migration page (existing /services page, if any; only describe migration help product-context.md says is offered, and never promise contract buyouts or services not listed there)
7. Pricing page (existing /pricing page)

**Bidirectional link rule:** Every spoke links back to pillar via descriptive anchor; pillar links to every spoke; alternatives listicle links to pillar 4 times via "compare" anchor.

**Voice rule:** The voice of the voice owner named in product-context.md (read the brand voice skill named in product-context.md, if one is installed, otherwise the Voice section of product-context.md, before any writing). No em dashes. No ampersands. Benefits-first. Concession lines for competitors are mandatory (3+ per pillar).

**Schema rule:** Article + FAQPage JSON-LD on every cluster page. Pillar adds Product schema. Customer stories add Review schema (when added).

The planner skill has the full template library; refer to it rather than memorizing details here.
