---
name: blog-manager-sop
description: Static SOP document for the human blog manager running a hub-and-spoke publishing cycle for the product described in product-context.md. This is not an agentic skill. It is a reference document the blog manager reads to understand how the cluster system works, what publishes when, in what order, and how to handle exceptions. Trigger on phrases like "what's the publishing cycle", "how does the hub-and-spoke model work", "when should I publish post 4", "what's the SOP for clusters", "blog manager handbook", or any question about the operational publishing rhythm. The blog manager (whoever owns publishing on the team) consults this doc before every publish action.
---

# Blog Manager SOP: Hub-and-Spoke Publishing Cycle

This is the operational handbook for the human running content publishing for {{PRODUCT}}. It explains how a cluster moves from "written" to "published" to "tracked" to "optimized," what dependencies exist between posts, and how to handle the things that go wrong.

Read this before publishing any cluster post. Re-read every time something feels off.

## Who this is for

The content/blog manager (whoever owns publishing). This document assumes you have admin access to the {{PRODUCT}} blog CMS, the cluster Google Sheet, Google Search Console, and the cluster output folder where Claude has written posts.

This document does not assume you're an SEO expert. It assumes you can follow steps and recognize when something needs escalation.

## What is hub-and-spoke?

A cluster is one **pillar** post (the hub) plus 4 to 8 **spoke** posts that orbit it. Every spoke links to the pillar with descriptive anchor text, and the pillar links to every spoke. The result is a tightly bound graph that signals topical authority to Google and to AI engines (ChatGPT, Perplexity, Bing Copilot, etc.).

A typical cluster has 8 posts:

1. **Pillar**: `In-depth: {{PRODUCT}} vs [Competitor]` (BOFU, the hub)
2. **Listicle**: `The most popular [Competitor] alternatives & competitors, compared` (MOFU)
3. **Migration guide**: `Migrate from [Competitor] to {{PRODUCT}}` (BOFU)
4. **Narrative**: `Why I switched from [Competitor] to {{PRODUCT}}` (TOFU/MOFU)
5. **Pricing explainer**: `[Competitor] pricing explained` (TOFU)
6. **3-way comparison**: `[Competitor] vs [Other] vs {{PRODUCT}}` (MOFU)
7. **B2B review**: `Is [Competitor] good for B2B enterprises? An honest review` (TOFU/MOFU)
8. **Category explainer**: `[Category A] vs [Category B] for B2B` (TOFU)

Customer story spokes (e.g. "How Initech built their content engine on {{PRODUCT}}") get added when product-context.md lists a named, written-permission customer who switched from the competitor in question.

## The publishing cadence

Clusters publish in **two waves** with a 7-day gap between them.

### Wave 1: Co-launch (Day 0)

Three posts publish on the same day:
- **Pillar (post 1)** — the hub
- **Alternatives listicle (post 2)** — catches the highest-volume MOFU traffic
- **Pricing explainer (post 5)** — catches the broadest TOFU traffic

These three are co-launched because they cross-reinforce. The pillar links to listicle and pricing, the listicle links to pillar and pricing, and the pricing post links back to pillar. Google sees a fully wired-up topical graph from day 1.

### Wave 2: Spoke fill-in (Day +7)

The remaining four posts publish on Day 7:
- **Migration guide (post 3)**
- **3-way comparison (post 6)**
- **B2B review (post 7)**
- **Category explainer (post 8)**

The narrative (post 4) ships when a guest author or willing customer is identified. There is no fixed deadline for this one.

### Why the two waves?

PostHog ships clusters this way for two reasons. First, it gives Google's crawler a chance to discover and index the hub before the spokes flood in, which seems to help the pillar establish topical authority before the spokes start ranking. Second, it gives the human team a sanity check: if Wave 1 has a critical error, you catch it in the first 7 days before it propagates across all 8 posts.

## Pre-publish checklist (per post)

Before clicking publish on any post:

- [ ] **QA report exists and verdict is PASS or PASS WITH MINOR FIXES.** Find this at `clusters/[cluster-name]/qa-reports/[slug]-qa-report.md`. If verdict is FAIL, do not publish. Send back to the writer.
- [ ] **Visual assets are in place.** Check `clusters/[cluster-name]/images/[slug]/`. Each post should have at least one hero image and one diagram. If visuals are missing, the visual-designer skill needs to run before publish.
- [ ] **Internal links are bidirectional.** The link-validator skill should have left a green report at `clusters/[cluster-name]/link-validation-report.md`. If the pillar publishes before the spokes, the spoke links from pillar will 404 until the spokes go live. Plan accordingly (see "Sequencing" below).
- [ ] **Slug matches GSC tracking.** The slug declared in the frontmatter must match the published URL. If the CMS auto-modifies slugs, check after publish.
- [ ] **Schema markup renders.** After publish, paste the published URL into Google's Rich Results Test (https://search.google.com/test/rich-results) and confirm Article, FAQPage, BreadcrumbList, or HowTo schemas render without errors.
- [ ] **No broken images.** Open the published post on mobile and desktop. Confirm every image loads.
- [ ] **Boilerplate footer present.** Final visual confirmation that the standard {{PRODUCT}} boilerplate is at the bottom of the post.

## Sequencing (avoiding 404s)

Because the pillar links to spokes that haven't shipped yet, here's the clean sequencing for Wave 1 (Day 0):

1. **Step 1:** Publish the **listicle** first. (Pillar links to it, listicle is a sibling.)
2. **Step 2:** Publish the **pricing explainer** second. (Pillar and listicle both link to it.)
3. **Step 3:** Publish the **pillar** last. (Now all its spoke links resolve.)

For Wave 2 (Day 7), publish in any order. All four are siblings of each other and the pillar already exists.

If you have to publish a single post out of cycle (e.g. urgent customer ask for the migration guide), publish the destination spoke first, then update the pillar's link to it.

## What to do when QA flags hard violations

When you read the QA report and see verdict FAIL:

1. **Do not edit the post yourself.** Send the QA report back to the writer (or to Claude in the cluster-writer thread) with the message: "QA returned [N] hard violations. Please re-run the writer pass and re-submit for QA."
2. **Track the cycle.** In the cluster Google Sheet, set the post's `qa_status` to `FAIL — cycle 2 in progress`. Increment cycle count each round.
3. **Maximum 3 cycles per post.** If a post fails QA three times, escalate to the cluster orchestrator owner. Something is structurally wrong with the plan, the SERP analysis, or the writer's understanding of the product as described in `product-context.md`.

## What to do when something breaks after publish

### Broken internal link
Don't panic. Open the post in CMS, fix the link, save. Update the link-validation report at `clusters/[cluster-name]/link-validation-report.md` to note the post-publish fix.

### Schema not rendering
Most common cause: CMS strips out the JSON-LD blocks. Ask your CMS admin to confirm Article and FAQPage schemas are being rendered server-side. Re-test with Rich Results Test.

### Pillar getting more traffic than expected, spokes getting less
This is normal in the first 30 days. Pillars catch most of the link equity early. By day 60–90, well-formed clusters distribute traffic across all 8 posts. The performance-tracker skill (15-day cadence) will surface this.

### Spoke ranking but pillar isn't
The performance-optimizer skill will catch this and recommend specifically what to update. Common cause: pillar word count too low, or pillar missing too many internal links.

### Competitor takes the SERP back
Unavoidable on some queries. The performance-optimizer will recommend either (a) adding a new spoke that targets a narrower keyword, (b) updating the existing spoke with refreshed content, or (c) accepting the loss and refocusing on adjacent keywords.

## The 15-day performance check (cadence)

Every 15 days, the performance-tracker skill runs:

1. It pulls the latest GSC data (clicks, impressions, CTR, avg position) for every URL in the cluster
2. It updates the cluster Google Sheet with the new numbers
3. It flags posts that moved up or down significantly
4. It generates a monthly report at `clusters/[cluster-name]/monthly-reports/[YYYY-MM-DD].md`

The blog manager reads this report every cycle. If a post is flagged as significantly degraded (lost 30%+ of clicks vs. previous cycle), the performance-optimizer skill runs and produces specific recommended changes.

You implement the recommended changes (or push back through the cluster-writer for substantial rewrites). Then the cycle repeats.

## When the performance-optimizer recommends changes

The optimizer outputs change recommendations like:

- "Add 5 FAQ questions on [topic] to pillar — these are emerging PAA questions GSC shows we're not yet capturing"
- "Update H2 in listicle entry 2 to match the higher-CTR title pattern from GSC data"
- "Add a comparison table to the [section] in the migration guide — competitor pages with this table rank higher for migration intent"
- "The spoke at /blog/[slug] has lost 40% of impressions. Recommend a content refresh per the 8 specific changes listed."

Your job as blog manager:

1. Read the recommendations
2. For minor changes (FAQ additions, anchor text updates, schema fixes), implement directly in CMS
3. For major changes (full content refreshes, new spokes, repositioning), send back to the cluster-writer for a v2 draft, then run QA, visual, link-validation again before publishing the updated post

## Escalation paths

| Situation | Escalate to | Why |
|---|---|---|
| Post fails QA 3 times | Cluster orchestrator owner | Plan or `product-context.md` may be wrong |
| Performance degraded across 5+ posts in cluster | Cluster orchestrator owner + cluster-writer skill owner | Something systemic: possibly competitor moved, possibly {{PRODUCT}} positioning changed |
| Customer logo on a post needs to be removed (PR or contractual reason) | Marketing lead + legal | Unblock immediately, fix in cluster after |
| Schema validator throws errors after publish | CMS admin | Likely a server-side rendering issue |
| GSC shows manual penalty | SEO lead + cluster orchestrator owner | Severe: likely a single-post issue, but cluster-level audit needed |

## Adjacent doc: customer story spokes (when applicable)

When product-context.md lists a named customer who switched from a competitor and granted permission to be referenced, a customer story spoke can be added to the cluster. This is post type "customer story" and follows a separate template:

- Title: `How [Customer] [Outcome] with {{PRODUCT}}`
- Schema: Article + Review (or Article only if no rating signal)
- Voice: First-person quotes from the customer + {{PRODUCT}} team narration
- Internal links: pillar + listicle + relevant migration guide
- Length: 1,000–1,500 words

Add this to the cluster Google Sheet as a new row when the customer agrees. Run the standard cluster-writer + QA + visuals + link-validation pipeline.

## File locations reference

```
clusters/
└── [cluster-name]/
    ├── README.md                              # cluster summary
    ├── cluster-plan.md                        # the plan
    ├── cluster-state.json                     # orchestrator state
    ├── serp-analysis.md                       # planner output
    ├── link-validation-report.md              # link-validator output
    ├── qa-reports/                            # quality-checker outputs (one per post per cycle)
    ├── monthly-reports/                       # performance-tracker outputs (one per 15-day cycle)
    ├── images/                                # visual-designer outputs (one folder per slug)
    └── posts/
        ├── [pillar-slug].md
        ├── [listicle-slug].md
        └── [other-spokes].md
```

The cluster Google Sheet (the single source of truth, replacing cluster-tracker.tsv) lives at:
`https://docs.google.com/spreadsheets/d/[CLUSTER_SHEET_ID]/edit`

The blog manager has edit access. The performance-tracker skill writes into it via the Sheets API.

## What the blog manager does NOT do

- Does not rewrite posts (that's the writer)
- Does not generate visuals (that's the visual-designer)
- Does not check schema correctness (that's the QA skill)
- Does not validate links (that's the link-validator)
- Does not pull GSC data manually (that's the performance-tracker, with manual CSV input every 15 days)
- Does not decide what changes to make based on GSC data (that's the performance-optimizer)

The blog manager's job is to execute the publish, verify the post is live and rendering correctly, and act on the recommendations the system produces.

## Quick-reference cheat sheet

| Step | Skill | Output | Blog manager action |
|---|---|---|---|
| 1 | cluster-orchestrator | Plan + state file | Note the cluster start date |
| 2 | cluster-planner | SERP analysis | Skim for ICP-divergence finding |
| 3 | cluster-writer | Draft .md files | Wait for QA |
| 4 | cluster-quality-checker | QA report | If PASS, proceed. If FAIL, push back. |
| 5 | visual-designer | SVG/PNG images in images/[slug]/ | Verify visually |
| 6 | link-validator | Link audit report | Verify GREEN before publishing |
| 7 | **PUBLISH** | Live URLs | Sequence per "Sequencing" rules above |
| 8 | (Day +15) performance-tracker | Updated Google Sheet + monthly report | Read report, queue follow-ups |
| 9 | (As triggered) performance-optimizer | Change recommendations | Implement minor changes; route major changes back to writer |

## Final note

This SOP is a living document. When something happens that this doc doesn't cover, the blog manager should add a section describing what happened, what was decided, and what the new rule is. Over time the SOP becomes the operational memory of the cluster system.
