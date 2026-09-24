# PostHog Mixpanel Cluster Template (Reference)

Independent analysis of public pages. Not affiliated with PostHog.

This is the canonical hub-and-spoke template this system replicates. Reverse-engineered from PostHog's live Mixpanel cluster in April 2026.

## The 14-URL PostHog Mixpanel Cluster

PostHog runs 14 URLs around the Mixpanel comparison topic. For a first cluster, we replicate the 4 high-leverage spokes and defer 3 spoke types until customer evidence exists.

### Spoke 1: Pillar comparison (BOFU), `/blog/{us}-vs-{them}`
The hub of the cluster. Every other spoke links to and from it.

**Structural template (in order):**
1. Hero image with H1
2. Author byline (two named authors with photos) + publish date + tag
3. One-sentence intro stating both products' category
4. Numbered 2-bullet definition pair (bold name + 1-sentence positioning each)
5. "How is {Us} different?", exactly 3 numbered differentiators (PostHog uses: all-in-one / built for developers / transparent pricing; this system generalizes as: breadth / audience fit / pricing principle, taken from the three differentiators in product-context.md)
6. First install/sign-up CTA
7. Comparison sections (one per product area):
   - 2-line intro
   - ✓/✗ feature table with row label, description, ✓/✗/Beta/Plan-name
   - "Good to know" callout with concession or extra benefit
8. Pricing comparison (3 tables: typical, low-volume, high-volume scenarios), real $ numbers required
9. "When to choose {Us} vs {Them}" decision block: "Want X? Go with {Us}." / "Prefer Y? {Them} is a solid choice."
10. "Recommendations by team type": 5 personas, 1 sentence + winner each
11. Free-tier table
12. Second install CTA (same component repeated)
13. FAQ: 18-22 questions, real user-query phrasing, 40-110 word answers, bolded **Yes,** / **No.** lead-ins
14. Newsletter sub component
15. Boilerplate footer paragraph with all product hub links
16. Community questions / "Ask AI" component

**Word count:** ~5,000
**Schema:** Article + FAQPage + BreadcrumbList + Product
**Internal links:** ~70+
**External outbound:** Almost zero (closed garden), Mixpanel itself NOT linked

**Voice patterns to replicate:**
- First person plural ("we")
- Heavily opinionated but with concessions
- Short declarative sentences
- Frequent "you" + "we" framing
- Active voice dominant

### Spoke 2: Alternatives listicle (MOFU), `/blog/best-{them}-alternatives`
Catches "X alternatives" search intent. Always lists {Us} first.

**Structural template:** Always 4 main entries + 5 honorable mentions. Each main entry uses identical 6-block template:
```
## {N}. {Tool name}
[screenshot]
### What is {Tool}?
### Key features (bullets)
### Who uses {Tool}? (bullets + customer logos)
### How does {Tool} compare to {Them}? [mini ✓/✗ table]
[Main differences, 5 bullets] [Main similarities, 5 bullets]
### Why do companies use {Tool}?
[According to G2 reviews... + direct quote]
> #### Bottom line
> [1-sentence verdict]
```

**Word count:** ~3,500
**FAQ:** 14 questions, recycled from pillar with different phrasing
**G2/external citations:** YES, uses "According to G2 reviews" with real quotes
**Internal links:** ~50, including 4 separate "compare" anchor links UP to pillar

### Spoke 3: Migration tutorial (BOFU), `/docs/migrate/{them}` or `/tutorials/{them}-to-{us}`
Captures BOFU "how do I switch" intent. Pure technical voice.

**Structural template:**
```
[Banner: "Use our managed migration instead", upsell to product]
[Crosslink banner: "Read our comparison of {Us} vs {Them}", push to pillar]
## Gathering details
1. From {them}: API key, account ID
2. From us: API key, host
## Setting up the script
[git clone / .env example / run command / screenshots]
## What the tool is doing
1-5 numbered steps + schema-mapping bullets
```

**Word count:** ~1,200
**Voice:** Technical, second-person imperative ("Go to Organization Settings, click...")
**For {{PRODUCT}}:** Embed help center or knowledge base articles from {{DOCS_URL}} for technical specifics. The upsell banner only describes migration help listed in product-context.md.

### Spoke 4: Narrative testimonial (TOFU/MOFU), `/blog/why-i-{verb}-{them}-for-{us}`
First-person opinionated post. Captures "I'm frustrated with X" search intent.

**Structural template:**
- First person singular voice (different from pillar's first-plural)
- Dated guest-author preferred for authenticity
- 2 italic pull-quote callouts
- Code/diff blocks if technical (PostHog example: Mixpanel.track → posthog.capture)
- Sends traffic to alternatives listicle + pillar

**Word count:** ~900
**For a first cluster:** Mark as OPTIONAL until a customer/user is willing to write under their name

### Spokes 5, 6, 7: DEFERRED for a first cluster
- Customer story 1, 2, 3 (`/customers/{brand}`): defer if product-context.md lists no customer stories approved for use
- Services concierge page: if product-context.md lists a services or migration-help page, add competitor-switching language there during pillar publication, describing only the help product-context.md says is offered

## Recurring components (codified for skill reuse)

| Component | Purpose | When to insert |
|---|---|---|
| `<ComparisonHeader>` | Author byline + date + tag | Below H1 |
| `<DefinitionPair>` | Bold name + 1-sentence positioning ×2 | After intro |
| `<DifferentiatorList numbered>` | "How is {Us} different?" 3 bullets | Before first CTA |
| `<InstallCTA>` | Sign-up / install snippet | After intro and before FAQ |
| `<FeatureTable>` | ✓/✗ table | Once per product area |
| `<GoodToKnowCallout>` | Green callout, concession or benefit | After every feature table |
| `<PriceLadderTable>` | 6-row $ table at fixed volumes | After feature comparison |
| `<TeamRecommendationTable>` | 5-persona block | Before FAQ |
| `<FreeTierTable>` | Free allowances | After recommendations |
| `<FAQAccordion>` | 18-22 Q&A pairs, FAQPage schema | Before footer |
| `<BoilerplateFooter>` | "What is {Us}" + all product links | End of every cluster page |
| `<CrosslinkToPillar>` | "Read our comparison of X vs Y" | Top of every spoke |

## Bidirectional link rule

Pillar → every spoke (descriptive anchor)
Every spoke → pillar (via "Read our comparison" callout, top of page)
Pillar → migration guide (BOFU push, in pricing/decision section)
Migration guide → pillar (top banner, re-confirmation)
Alternatives listicle → pillar (4 separate "compare" anchor links)
Alternatives listicle → migration guide (in FAQ)

## Concession pattern (E-E-A-T booster)

Every pillar contains 3+ explicit "Choose {Them} if..." lines tied to real competitor strengths. The PostHog pillar does this three ways (paraphrased, not quoted):
- A "Consider keeping {Them} if..." line naming a specific advanced feature the reader may already be invested in.
- A "Prefer X? {Them} is a solid choice." line conceding a UI or audience the competitor serves better.
- A plain statement that the competitor's approach is better for a narrow use case with an existing, external data setup.

Concessions raise perceived neutrality and make pro-{{PRODUCT}} claims feel earned.

## Co-launch cadence

PostHog publishes pillar + alternatives listicle SAME DAY with same author byline. They update both when competitor product changes happen. Migration guide can lag by 7 days. Customer stories drop in over months.

## Banned content patterns (do NOT replicate)

- Ring-of-tools illustrations (PostHog avoids; we should too)
- Generic stock comparison images
- Marketing fluff in factual sections (the FAQ and tables stay neutral; brand voice goes in callouts only)
- Title Case in headings (PostHog uses sentence case)
- Em dashes (house rule, stricter than PostHog)
- Ampersands in body copy (house rule)
