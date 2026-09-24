---
name: cluster-planner
description: SERP analyst and content planner for competitor comparison clusters ({{PRODUCT}} vs {Competitor}). Use this skill when the orchestrator delegates planning, OR when the user asks to "plan a cluster", "research SERPs for", "do keyword research for", "analyze competitors for", "build a content plan for", or wants a content brief for a competitor comparison topic. This skill performs deep SERP analysis on parallel competitor pairs (since head-to-head {{PRODUCT}} vs X queries usually have no organic data), pulls People Also Ask, identifies content gaps, references the product's help center or knowledge base, and outputs a complete cluster plan as both markdown and a Google Sheets-compatible TSV tracker. It reads product-context.md for every product fact and runs a short intake to create it if missing. Trigger this skill aggressively for ANYTHING involving SERP research, content planning, keyword analysis, or hub-and-spoke architecture for competitor comparison clusters. The output is the input to cluster-writer, so do NOT skip this step.
---

# Cluster Planner

## Overview

This skill is the analyst layer of the cluster system. It takes a competitor name (e.g. "Globex") and produces a complete content plan that the writer skill can execute against.

The planning method is **parallel-competitor SERP mining**: since "{{PRODUCT}} vs Globex" has no organic search history, the planner analyzes how Globex shows up in OTHER comparisons (Globex vs another competitor from the competitor set, and so on) to extract the SERP playbook those competitors use, then maps it onto the PostHog Mixpanel template structure for {{PRODUCT}} to adopt.

## Inputs the orchestrator will provide

- `competitor_name` (e.g. "Globex")
- `cluster_slug` (e.g. "{{PRODUCT_SLUG}}-vs-globex")
- `vertical_context` (optional, e.g. a target industry or buyer segment from the ICP in product-context.md)
- `persistence_path` (where to write outputs)

If invoked directly without an orchestrator, ask the user for these inputs upfront in a single multi-question prompt.

## Phase 0: Load product context

Before anything else, read `product-context.md` at the root of the working folder (next to `clusters/`). It is the single source of truth for the product name, first-mention rule, website, help center URL, positioning, ICP, the three differentiators, verified proof points, verified capabilities by product area, pricing, what the product does NOT have, migration help actually offered, voice, and the competitor set.

If `product-context.md` is missing, copy the blank template at `references/product-context-template.md` to the root of the working folder, then fill it in with the user in one short intake (all sections asked together, not one question at a time) before planning. Do not start Phase 1 until the file exists and has at least brand basics, positioning, verified capabilities, pricing and the competitor set filled in.

## Workflow

### Phase 1: Identify parallel competitor pairs

Before any SERP work, identify the 3-5 most relevant parallel competitor pairs in the same category. Take the competitor set from the "Competitor set" table in product-context.md. For each cluster, this means:

- For a "Globex" cluster: parallel pairs are Globex vs each of the 3-4 other competitors in the competitor set that share its category or ICP, plus 1 pair between two of those other competitors if it has strong SERP volume.
- For a cluster on any other competitor: repeat the same pattern, pairing that competitor with the closest 3-4 names in the competitor set.
- If the competitor set is thin (fewer than 3 names), add the competitors that appear most often in the "vs" and "alternatives" SERPs for the target competitor, and flag them to the user as additions to product-context.md.

Confirm the parallel pair list with the user before running expensive SERP work, OR if the orchestrator passed `auto_proceed = true`, skip confirmation.

### Phase 2: SERP analysis on parallel pairs

For each parallel pair, run a `web_search` query and analyze the top 5 organic results. For each top result, capture:

- URL
- Title tag (and length in characters)
- Meta description (and length)
- H1
- H2 structure (extract all H2s)
- Approximate word count
- Number of comparison tables visible
- FAQ count
- Schema types likely (Article, FAQPage, Product, Review)
- Top external citations (G2, Capterra, Trustpilot, Reddit)
- Internal linking pattern (links back to brand pillar? Cross-links to alternatives lists?)

Use `web_fetch` on the top 2-3 results per pair if titles alone don't tell you enough. Be selective with web_fetch, it's expensive.

### Phase 3: People Also Ask + SERP features extraction

For the primary head-to-head keyword ("{{PRODUCT}} vs {Competitor}") AND each parallel pair, extract:

- People Also Ask questions (top 8)
- Featured snippet content if present
- AI Overview content if present
- Video carousel topics
- "Searches related to" terms

These become the FAQ raw material and the secondary keyword list.

### Phase 4: Knowledge base cross-reference

For tutorial and utility content (migration guides, integration docs, technical how-tos), search {{DOCS_URL}} (the product's help center or knowledge base, as listed in product-context.md) for relevant articles. Note which KB articles support which planned spoke. The migration guide spoke usually pulls heavily from KB.

Do NOT use KB articles as source material for blog post content. KB is for technical/utility supporting links only.

### Phase 5: Content gap analysis

Compare top-5-ranking posts on parallel pairs against the PostHog Mixpanel template (in `references/posthog-template.md`). Identify gaps:

- Which PAA questions are NOT answered well by any top-5 post?
- Which feature comparisons are missing or shallow?
- Which pricing scenarios are not covered?
- Where do top-5 posts use marketing fluff instead of concrete numbers?
- Which AEO-optimized blocks are missing (50-word answer, comparison tables, FAQPage schema)?

Each gap becomes a competitive advantage opportunity for {{PRODUCT}}'s posts.

### Phase 6: GSC keyword data check

Try to read the user's Google Search Console data via the connected GSC tool (if available). Pull:
- Top 50 queries the user's domain currently ranks for
- Queries with high impressions but low CTR (AI Overview opportunity)
- Queries with positions 11-30 (page 2, easy wins)

**If GSC is not connected:**
1. Output a file `keyword-list-for-gsc.txt` containing comma-separated keyword lists, one line per planned post
2. STOP and tell the user:
   > "Google Search Console isn't connected. I've written `keyword-list-for-gsc.txt` with the keywords I want to validate per planned post. Please pull screenshots from GSC for these keywords and paste the data back. I'll resume planning once you provide the data."
3. Do not proceed to Phase 7 until user provides data.

### Phase 7: Generate the cluster plan

Produce three artifacts in the cluster folder:

#### Artifact 1: `cluster-plan.md`

Structure:

```markdown
# Cluster Plan: {{PRODUCT}} vs {Competitor}

## Summary
[3-sentence overview: target keyword, parallel pairs analyzed, key SERP gap {{PRODUCT}} will exploit]

## Parallel SERP analysis
[Table summarizing top-5 ranking posts across parallel pairs]

## People Also Ask raw material
[All PAA questions captured, grouped by parallel pair]

## Content gap opportunities
[3-5 specific gaps {{PRODUCT}}'s posts will exploit]

## Planned posts (in priority order)

### Post 1: Pillar, {{PRODUCT}} vs {Competitor}
- **URL slug:** `/blog/{{PRODUCT_SLUG}}-vs-{competitor-slug}`
- **Funnel:** BOFU
- **Word count target:** ~5,000
- **Primary keyword:** {{PRODUCT}} vs {competitor} (lowercase, as searched)
- **Secondary keywords:** [list 8-12]
- **H1:** "In-depth: {{PRODUCT}} vs {Competitor}" (apply the first-mention rule in product-context.md)
- **Title tag (under 60 chars):** "{{PRODUCT}} vs {Competitor}: in-depth tool comparison"
- **Meta description:** [drafted, 150-160 chars]
- **H2 outline:**
  - Intro + numbered definition pair
  - "How is {{PRODUCT}} different?" (the 3 differentiators from product-context.md)
  - Platform comparison
  - [Area-by-area comparison: one comparison section per product area listed under "Verified capabilities" in product-context.md]
  - Pricing comparison (3 tables)
  - Security and compliance
  - When to choose {{PRODUCT}} vs {Competitor}
  - Recommendations by team type
- **FAQ list (18-22 questions):** [drafted, each with primary keyword strategy]
- **Internal link plan:**
  - Up to 6 product area pages (one per product area in product-context.md)
  - Down to alternatives listicle
  - Down to migration guide
  - Down to narrative post (when written)
- **Concession lines (3+ required):** [drafted]
- **Pricing scenarios:** [3, typical case, low case, high case with real numbers from the Pricing section of product-context.md]
- **KB articles referenced:** [list]

### Post 2: Alternatives listicle, Best {Competitor} alternatives
- **URL slug:** `/blog/best-{competitor-slug}-alternatives`
- **Funnel:** MOFU
- **Word count target:** ~3,500
- **Primary keyword:** {competitor} alternatives
- **Secondary keywords:** [list]
- **Main entries (4):** {{PRODUCT}} (always #1), [3 others identified from SERP]
- **Honorable mentions (5):** [identified from SERP]
- **FAQ list (12-16 questions):**
- **Internal link plan:** [4 links UP to pillar via "compare" anchor]

### Post 3: Migration guide, Migrate from {Competitor} to {{PRODUCT}}
- **URL slug:** `/blog/{competitor-slug}-to-{{PRODUCT_SLUG}}-migration` (or `/docs/migrate/{competitor-slug}`)
- **Funnel:** BOFU
- **Word count target:** ~1,200
- **Primary keyword:** {competitor} to {{PRODUCT}} migration
- **KB articles to embed:** [from {{DOCS_URL}}]
- **Migration help described:** [only what the "Migration help actually offered" section of product-context.md lists]
- **Cross-link plan:** Top: pillar comparison; mid: services page (if product-context.md lists one)

### Post 4: Narrative, Why I switched from {Competitor} to {{PRODUCT}}
- **URL slug:** `/blog/why-i-switched-from-{competitor-slug}-to-{{PRODUCT_SLUG}}`
- **Funnel:** TOFU/MOFU
- **Word count target:** ~900
- **Primary keyword:** switched from {competitor} to {{PRODUCT}}
- **Status:** OPTIONAL for a first cluster; ideal as guest post once a customer is willing

### Customer stories (deferred)
- 3 customer story slots reserved at `/customers/{brand}` once product-context.md lists named customer stories approved for use. Defer customer story spokes if product-context.md lists no customer stories.

## Co-launch cadence
PostHog publishes pillar + alternatives listicle on the SAME DAY with the same author byline. Recommend {{PRODUCT}} do the same: pillar + listicle launch together, migration guide 7 days later.

## Image plan per post
[Visual designer will produce 2 SVGs per post; suggested types listed]
```

#### Artifact 2: `cluster-tracker.tsv`

Tab-separated values, Google Sheets-compatible. Columns:

```
post_title	slug	primary_keyword	secondary_keywords	funnel_stage	word_count_target	status	internal_links_to	internal_links_from	kb_articles	publish_date	last_updated	visuals	qa_status
```

One row per planned post. Initial status = `planned`. The writer skill updates `status` to `written`, QA updates `qa_status`, link validator populates `internal_links_to` / `internal_links_from`.

#### Artifact 3: `serp-analysis.md`

The full SERP audit data: every top-5 post on every parallel pair, with title, word count, FAQ count, table count, schema, citations. This is the working notebook the writer references when drafting.

### Phase 8: Hand-off to orchestrator

Update `cluster-state.json` (the orchestrator's state file):
- `stages.planning = "complete"`
- `posts[]` = populated array with planned slugs and target word counts

Tell the orchestrator (or user, if running standalone):
> "Planning complete. Cluster plan written to {path}/cluster-plan.md. Tracker at {path}/cluster-tracker.tsv. Ready to invoke cluster-writer for the pillar post."

## Critical rules

- **Always load product-context.md first.** If it is missing, run the one-intake setup from the template before planning. Never plan against assumed product facts.
- **Always do parallel-pair SERP analysis first.** Don't try to analyze "{{PRODUCT}} vs X" directly, there's no organic data. Mine the parallel competitor SERPs.
- **Always extract real PAA, not invented questions.** PAA is your FAQ raw material. Invented questions don't match search intent.
- **Always identify gaps, not just patterns.** The plan is useless if it just copies what top posts already do. Find what they miss.
- **Halt on missing GSC.** Don't fabricate keyword data. Halt and ask the user for GSC screenshots.
- **Never reference KB as content source.** KB is utility-only. Blog post content comes from SERP analysis, PAA, and the product knowledge in product-context.md.
- **Always output ALL three artifacts**: plan, tracker, SERP analysis. Skipping any breaks downstream skills.

## Reference files

- `product-context.md` (root of the working folder, next to `clusters/`): the product's areas, ICP, pricing, positioning, proof points, competitor set and voice. Pulled into every plan. If missing, create it from `references/product-context-template.md` with the user in one short intake.
- `references/product-context-template.md`: the blank template for product-context.md. Do not edit it; copy it.
- `references/posthog-template.md`: The full PostHog Mixpanel cluster teardown. Read this before generating the cluster plan to ensure structural fidelity.
- `references/voice-rules.md`: Voice rules for the voice owner named in product-context.md + house style rules (no em dashes, no ampersands, etc.). Reference these when drafting H1s, FAQ questions, and concession lines. For the full voice, read the brand voice skill named in product-context.md, if one is installed, otherwise the Voice section of product-context.md.

## What NOT to do

- Do NOT write the actual blog post content. The plan is an outline + research, not draft prose. Save full writing for `cluster-writer`.
- Do NOT skip parallel-pair analysis even if it feels redundant. The PostHog playbook depends on it.
- Do NOT use stock H2 outlines. The H2 structure should reflect what top-ranking parallel-pair posts use, mapped onto the product areas listed in product-context.md.
- Do NOT propose customer story posts for a first cluster unless product-context.md lists customer stories or the user confirms they have named clients willing to be featured.
- Do NOT plan any capability, integration, customer or number that is not written in product-context.md. If it is not in the file, the product does not have it.
- Do NOT plan more than 5 posts in a first cluster. PostHog's 14-URL cluster includes 3 customer stories (deferred), 1 pricing page, 1 services page, 1 category index, 1 source artefact. A first cluster should be 4-5 active blog posts max.
