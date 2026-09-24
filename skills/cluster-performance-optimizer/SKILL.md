---
name: cluster-performance-optimizer
description: Analyzes published cluster posts against fresh GSC performance data and outputs specific, actionable change recommendations to improve rankings and AEO citations. Use this skill after the performance-tracker has produced a 15-day report, when the blog manager asks "what should we change to improve [post/cluster]", when a post has been flagged as significantly degraded, or when a cluster has had 3+ stable cycles and the team wants to push gains further. Trigger on phrases like "optimize this post", "what changes will improve ranking", "AEO recommendations", "GSC-based optimization", "improve cluster performance", or any request to translate GSC data into editorial action. This skill produces recommendations only. It does not edit content. The blog manager implements minor changes; the cluster-writer handles major rewrites.
---

# Cluster Performance Optimizer

This is the brain of the self-healing loop. It reads two things — the original post content and the latest GSC data — and outputs specific, actionable change recommendations. The blog manager implements minor changes directly. The writer skill handles substantial rewrites.

## When to use this skill

Trigger when:
- The performance-tracker has produced a fresh 15-day report and posts are flagged as "drop" or "plateau"
- The blog manager asks "what should we change on [post] to improve ranking"
- A cluster has had 3+ stable cycles and the team wants to push further
- An AEO / GEO audit is requested (recommendations specifically for AI-engine citation patterns, separate from Google ranking)
- A competitor moved (new feature, repositioning, ranking shift) and the cluster needs a defensive update

Required inputs:
- The latest performance-tracker report (`clusters/[cluster]/monthly-reports/[YYYY-MM-DD].md`)
- The cluster's published posts (`clusters/[cluster]/posts/*.md`)
- The cluster Google Sheet (read-only)
- `product-context.md` (the filled file at the working-folder root, next to `clusters/`), to make sure recommendations don't introduce invented features
- The PostHog template audit (`skills/cluster-writer/references/posthog-template-audit.md`) — for structural recommendations

If the performance-tracker hasn't run recently, halt and ask for a fresh check first. Optimizer recommendations are only as good as the data behind them.

## What the optimizer outputs

A single markdown file at `clusters/[cluster-name]/optimization-reports/[YYYY-MM-DD].md` with this structure:

```markdown
# Optimization Recommendations: [cluster name]

**Based on report:** monthly-reports/[YYYY-MM-DD].md
**Generated:** [date]
**Posts analyzed:** [count]
**Total recommendations:** [count]
**Severity breakdown:** [count by severity]

## Per-post recommendations

[For each post that has at least one recommendation, a section with:
- Current performance snapshot
- Top issues identified
- Specific changes recommended, ranked by expected impact
- Implementation owner (blog manager direct vs. writer rewrite)]

## Cluster-level recommendations

[Cross-post recommendations that affect more than one post:
- Internal link topology adjustments
- New spoke posts to add
- Existing spokes to merge or retire
- Schema-level improvements that span the cluster]

## AEO / GEO recommendations

[Recommendations specifically for AI-engine citation patterns (ChatGPT, Perplexity, Bing Copilot, Google AI Overviews):
- Question-format H2/H3 additions
- Citation-friendly structural changes
- Quotable claim formatting]

## Recommendations that need verification before implementing

[Things the optimizer wasn't sure about — e.g. "expand the FAQ on [topic] but verify topic is in product-context.md first"]

## Implementation queue

[A simple ordered list:
1. [Post] [Change] [Owner] [Estimated effort]
2. ...]
```

## The 8 recommendation categories

The optimizer produces recommendations in 8 categories. Every recommendation gets tagged with one of these so the blog manager knows whether to implement directly or route to the writer.

### Category 1: Title and meta optimization (BLOG MANAGER directly)

**Trigger pattern:** Position 4–10 with low CTR (<2%). The post is ranking but not earning clicks.

**Recommendation pattern:**
- Test a sharper meta description that incorporates the top 1–2 actual queries from GSC (not the planning keywords)
- Test an H1 variant that more directly answers the dominant query intent
- Add the year to the meta title only (NEVER the H1 — see PostHog rule 2)

**Example output:**
> **Post:** /blog/best-globex-alternatives
> **Issue:** Position 6.2, CTR 1.8%, 4,103 impressions over 15 days
> **Top query GSC shows:** "globex competitor for B2B" (not in current meta description)
> **Change:** Update meta description to: "Compare Globex competitors for B2B teams. {{PRODUCT}}, Initech, Umbrella, and Hooli compared on features, pricing, and verdicts."
> **Owner:** Blog manager (direct CMS edit)
> **Estimated effort:** 5 minutes

### Category 2: FAQ additions (BLOG MANAGER directly, if simple; otherwise WRITER)

**Trigger pattern:** GSC shows queries with significant impressions where the post ranks position 11–25 — these are "almost there" queries that probably need an FAQ answering them directly.

**Recommendation pattern:**
- For each high-impression query that's not directly answered in the existing FAQ, add a question + 2-3 sentence answer
- Keep FAQ format consistent with PostHog pattern (question on its own line, answer paragraph below)
- If the answer requires net-new product claims, route to writer for verification against product-context.md

**Example output:**
> **Post:** /blog/{{PRODUCT_SLUG}}-vs-globex
> **Issue:** GSC query "globex vs {{PRODUCT}} for agencies" has 312 impressions, 0 clicks, position 18
> **Change:** Add FAQ question: "Is {{PRODUCT}} good for agencies?" with answer covering: yes for multi-client agencies (if product-context.md lists that use case), maybe not for solo freelancers (Globex is better there). Verify the framing against product-context.md.
> **Owner:** Writer (because answer requires product positioning judgment)
> **Estimated effort:** 30 minutes including QA cycle

### Category 3: Internal link injection (BLOG MANAGER directly)

**Trigger pattern:** GSC shows a query that's better answered by a sibling spoke than by the post currently ranking for it. Internal link injection redirects the click intent.

**Recommendation pattern:**
- Identify the under-ranking sibling spoke that better matches the query
- Identify the over-ranking post where the query currently lands
- Add an internal link from the over-ranking post to the under-ranking sibling, with anchor text matching the query

**Example output:**
> **Issue:** Query "globex pricing breakdown" lands on /blog/{{PRODUCT_SLUG}}-vs-globex (pillar) at position 8, but /blog/globex-pricing-explained should rank for this and currently doesn't.
> **Change:** In the pillar's "Price comparison" section, add inline anchor: "...see our [full Globex pricing breakdown](/blog/globex-pricing-explained) for plan-by-plan numbers."
> **Owner:** Blog manager (direct CMS edit)
> **Estimated effort:** 5 minutes

### Category 4: Comparison table additions (WRITER)

**Trigger pattern:** A direct competitor or sibling post is ranking higher and has more comparison tables. PostHog's pattern shows comparison tables correlate strongly with ranking and AI-engine citations.

**Recommendation pattern:**
- Identify the topic the competitor's higher-ranking post has tables for
- Recommend adding 1–2 tables to the {{PRODUCT}} post on the same topic
- Provide the suggested table headers and the rows count expected
- Route to writer because new tables require verification against product-context.md

**Example output:**
> **Post:** /blog/{{PRODUCT_SLUG}}-vs-globex
> **Issue:** Competitor pillar at position 4 has a "Customer support" comparison table. The {{PRODUCT}} pillar doesn't, and the gap is showing up in queries like "globex customer support."
> **Change:** Add a "Customer support" subsection with comparison table: support channels (email, chat, phone, dedicated CSM), response time, knowledge base depth, community forum.
> **Owner:** Writer (verify against product-context.md)
> **Estimated effort:** 1–2 hours including QA cycle

### Category 5: Schema enrichment (BLOG MANAGER, with CMS admin support)

**Trigger pattern:** Schema validator shows missing or incomplete schema. Or AI Overviews are ranking competitor content but not {{PRODUCT}}'s, suggesting {{PRODUCT}}'s schema isn't AI-engine friendly.

**Recommendation pattern:**
- Check declared schema in frontmatter vs. what's actually rendering
- Recommend adding speakable, mainEntityOfPage, or HowTo step granularity if missing
- Recommend adding Article schema's `articleSection` field

**Example output:**
> **Post:** /blog/migrate-from-globex-to-{{PRODUCT_SLUG}}
> **Issue:** HowTo schema is declared but step-level structured data isn't rendering — Google sees one HowTo block instead of nine numbered Step blocks.
> **Change:** Have CMS admin add Step-level JSON-LD nesting: each `<h2>Step N</h2>` should produce a HowToStep schema with `name` and `text`.
> **Owner:** Blog manager + CMS admin
> **Estimated effort:** 30 min coordination, 1 hour CMS work

### Category 6: Content refresh (WRITER, full pass)

**Trigger pattern:** Post has lost 30%+ of clicks across two consecutive cycles, or position has dropped 5+ spots, and the GSC queries indicate the topic itself has shifted (e.g. competitor renamed a product, market repositioned).

**Recommendation pattern:**
- Route the post back to the writer with a refresh brief
- Specify what's stale: claims, competitor names, pricing numbers, customer logos, feature lists
- Specify what queries are emerging that the post should now address
- Re-run the full QA cycle on the refreshed version

**Example output:**
> **Post:** /blog/best-globex-alternatives
> **Issue:** Lost 42% of clicks over two cycles. Top declining queries: "globex alternatives for europe" (which competitor X has overtaken), "best alternatives to globex for agencies" (which now favors product Y).
> **Change:** Full refresh. Update entries 2 and 3 (Initech, Umbrella) with current pricing. Add a new entry covering competitor X. Update intro to address Europe-specific positioning explicitly. Re-run QA → visuals → link validation.
> **Owner:** Writer (full cycle)
> **Estimated effort:** 4–6 hours

### Category 7: New spoke addition (WRITER + ORCHESTRATOR)

**Trigger pattern:** GSC shows a high-volume query with 0 ranking position because the cluster has no post addressing it. This is an opportunity, not a degradation.

**Recommendation pattern:**
- Propose a new spoke in the cluster
- Provide the title, slug, primary keyword, funnel stage, expected word count, and cross-link map
- Route to orchestrator to add to the cluster plan, then writer

**Example output:**
> **Issue:** Query "globex vs initech for agencies" has 1,847 impressions/month with no {{PRODUCT}} post ranking. Currently competitor's affiliate site ranks position 3.
> **Change:** Add new spoke: title "Globex vs Initech: which is right for agencies", slug `/blog/globex-vs-initech-for-agencies`, MOFU, ~2,000 words, links to pillar + 3-way comparison + listicle.
> **Owner:** Orchestrator (plan update) + Writer (draft) + full cluster pipeline
> **Estimated effort:** 1 day total cycle

### Category 8: Spoke retirement or merge (BLOG MANAGER + ORCHESTRATOR)

**Trigger pattern:** Spoke has had <50 clicks/cycle for 3+ consecutive cycles, AND its target query is now better covered by a sibling spoke.

**Recommendation pattern:**
- Recommend either (a) merging the underperformer's content into the better-performing sibling and 301 redirecting, or (b) retiring entirely if the topic isn't worth keeping
- Provide the redirect map and the merge plan

**Example output:**
> **Post:** /blog/[underperforming-slug]
> **Issue:** 31 clicks total over 6 cycles. Target query is now better answered by /blog/[sibling-slug].
> **Change:** Move the 2 useful sections from this spoke into the sibling, then 301 redirect this URL to the sibling.
> **Owner:** Blog manager (CMS work) + Orchestrator (cluster plan update)
> **Estimated effort:** 1 hour

## How the optimizer reasons

When reading the performance-tracker report and the post content, the optimizer follows this reasoning order:

### 1. Identify the actual query intent each post is winning

For each post, look at GSC's top 10 queries it ranks for. Group by intent:
- Direct comparison ("X vs Y")
- Alternative discovery ("X alternatives")
- Pricing ("X pricing", "X cost")
- Migration ("switch from X", "migrate from X")
- Suitability ("is X good for Y")
- General brand ("X review", "X features")

### 2. Identify the gap between intent and content

Does the post's content match the intent it's actually ranking for? Mismatches are the highest-impact opportunities:

- **Intent says "pricing" but post is a general comparison:** Add a pricing-focused section, or add a stronger internal link to the pricing spoke.
- **Intent says "for agencies" but post is generic B2B:** Add an agency-specific subsection or FAQ, or add an agency-specific spoke if the query volume justifies it.

### 3. Identify AEO / GEO patterns

AI engines (ChatGPT, Perplexity, Bing Copilot, Google AI Overviews) cite content that:
- Answers questions in the first 2 sentences of a section (don't bury the answer)
- Uses question-format H2 / H3 headings (so the AI parser knows what's being answered)
- Contains short, quotable factual statements with named entities and numbers
- Has FAQ sections with clear question + answer pairs
- Has comparison tables with labeled columns

If the post's structure makes it hard for an AI engine to extract a clean answer, the optimizer flags structural changes.

### 4. Check for "almost ranking" queries

GSC queries at position 11–25 with significant impressions are the highest-leverage targets. They have proven demand but the post isn't quite winning. Most often the fix is either an FAQ addition (Category 2) or a minor content addition that explicitly answers the query.

### 5. Check for invented-feature drift over time

As the product changes, posts can drift. The optimizer compares each post's current claims against the latest `product-context.md`. If a post claims a feature that's been removed, or a customer logo that's been retired, the optimizer flags this as a content refresh trigger (Category 6).

### 6. Cluster-level reasoning

After per-post analysis, the optimizer looks across the cluster:
- Are spokes cannibalizing each other? (Two posts ranking 3 and 4 for the same query suggests one should be merged or repositioned.)
- Is the pillar getting all the link equity but spokes underperforming? (Solution: add more cross-spoke internal links.)
- Is the cluster missing a TOFU entry point? (Solution: new spoke at Category 7.)
- Is there a spoke pulling the cluster's average position down significantly? (Solution: refresh or retire.)

## Constraints on recommendations

The optimizer must not recommend:
- Adding features {{PRODUCT}} doesn't have (verify against product-context.md)
- Buyout language (per writer rule 8)
- Year-stamped headlines (per writer rule 2)
- External links to competitor websites (per writer rule 3)
- Quick-answer blocks (per writer rule 1)
- Em dashes (per writer rule 4)
- Customer logos not on the live homepage (per product-context.md)
- More than 2 major rewrites per cluster per cycle (capacity constraint — would overload the writer)

If a recommendation needs one of these, the optimizer drops it and notes "skipped: would violate writer rule N."

## Severity ranking

Each recommendation gets a severity:

- **HIGH:** Implementing the change is expected to materially improve clicks or position within 30 days (e.g. fix a misaligned meta description, add an FAQ for a position-12 query with 800 impressions)
- **MEDIUM:** Implementation is expected to help but won't move numbers dramatically (e.g. add a comparison table on a niche topic)
- **LOW:** Worth doing for completeness or AEO hygiene but unlikely to move performance much (e.g. tighten an H3 wording)

The implementation queue at the end of the report is sorted HIGH → MEDIUM → LOW so the blog manager can stop work whenever capacity runs out.

## What this skill does NOT do

- Does not implement changes (blog manager and writer do that)
- Does not edit posts directly
- Does not generate new visuals (visual-designer's job)
- Does not run on a fixed cadence (it runs when triggered, typically after performance-tracker)
- Does not auto-update the cluster Google Sheet (only performance-tracker writes to the sheet)
- Does not invent features or claims (verifies everything against product-context.md before recommending)

## Sample call

```bash
cd clusters/{{PRODUCT_SLUG}}-vs-{competitor}

# Performance tracker has just produced a report:
ls monthly-reports/
# 2026-04-25.md

# Trigger optimizer
python -m performance_optimizer.run \
  --cluster {{PRODUCT_SLUG}}-vs-{competitor} \
  --report monthly-reports/2026-04-25.md \
  --product-context product-context.md
```

Output is a new file at `optimization-reports/2026-04-25.md`. Blog manager opens it, works through the implementation queue, and routes major changes back to the writer.

## How this connects to self-healing

This is the loop:

```
publish → performance-tracker (15-day cadence) → optimizer (recommendations)
   ↑                                                       ↓
   └─── writer (refresh) ← QA ← blog manager / orchestrator
```

The optimizer doesn't fix anything. It surfaces what to fix. The blog manager makes minor changes directly. Major changes go back through the writer + QA cycle. After the next 15 days, performance-tracker measures whether the changes worked. The optimizer reads the new data and recommends the next round.

Self-healing means: the system never declares a cluster "done." It declares cycles, measures, learns, and adjusts. Over 6+ cycles, a healthy cluster gets sharper at every level — content, structure, links, schema, AEO formatting.

The skill's job is to make that next adjustment specific and actionable, not abstract.
