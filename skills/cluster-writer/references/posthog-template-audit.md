# PostHog Mixpanel Cluster: Line-by-Line Audit

*Independent analysis of public pages. Not affiliated with PostHog.*

**Source posts audited:**
- Pillar: https://posthog.com/blog/posthog-vs-mixpanel
- Listicle: https://posthog.com/blog/best-mixpanel-alternatives

This file is the source of truth for what PostHog actually does (versus what AI-trained patterns assume PostHog does). It describes structure only. Quoted material is limited to short labels and heading patterns; everything else is paraphrased.

## Pillar audit (PostHog vs Mixpanel)

### Title and metadata
- **H1 title:** "In-depth: PostHog vs Mixpanel" (pattern: "In-depth: {Us} vs {Them}")
- **No year in title.** Date "Jan 28, 2026" appears only in metadata byline.
- **Authors:** two named authors with profile links
- **Tag:** "Comparisons"
- **TOC:** Auto-generated, sits below the byline

### Opening structure
1. **NO QUICK-ANSWER BLOCK.** Post opens directly with body content.
2. **First paragraph** (paraphrased structure): two sentences. The first places both products in the same category and names the shared job they do for teams, with the competitor name linked to PostHog's own alternatives post. The second notes that both have expanded beyond the core category but differ in approach and feature set.
3. **Note on the link:** "Mixpanel" links to PostHog's own listicle, NOT to mixpanel.com.
4. **Numbered definition pair** (paraphrased structure):
   > 1. **Mixpanel** gets one sentence listing its category plus its main add-on features, then one sentence naming the team types it is built for.
   > 2. **PostHog** gets one sentence positioning it as an all-in-one platform, then a single long sentence listing every product it offers, each one inline-linked to its own product page, ending with "and more."
5. **All product names in the definition pair are inline-linked to PostHog's own product pages.** Zero external links.

### "How is PostHog different?" H2 section
- Three numbered subsections with H3 headers:
  1. "1. We're an all-in-one platform"
  2. "2. We build for developers"
  3. "3. We promise transparent and cheap pricing (forever)"
- Each subsection is a positioning statement, not a feature list
- Subsection 1 includes a bulleted list of all PostHog products with inline links
- Subsection 3 openly pokes fun at the competitor's enterprise-sales culture, using a joke about a lavish annual conference. Self-deprecating, irreverent brand voice. **NOTE: {{PRODUCT}} content keeps a professional tone unless product-context.md says otherwise.**

### Capability comparison sections (H2 "Comparing PostHog and Mixpanel")
- Framing sentence (paraphrased structure): one sentence saying PostHog is more than a Mixpanel alternative and can also replace two other named tools for specific jobs. Every competitor name in it links to PostHog's own alternatives posts for that competitor.
- All competitor mentions link to PostHog's OWN alternatives posts.
- **9 H3 sub-comparison sections:**
  1. Platform
  2. Product analytics
  3. Website analytics
  4. Session replay (with H4 nested "Library support for replays")
  5. Feature flags
  6. Experiments
  7. Surveys
  8. Price comparison
  9. Data integrations
- Plus a 10th: "Security and compliance"

### Comparison table format (per H3 section)
- Two columns shown in the rendered HTML: PostHog (implied left), Mixpanel (right with "compare" link to PostHog's own pillar, which is recursive)
- Each row has:
  - **Bolded feature name** (often inline-linked to PostHog's own product/docs page)
  - One-line description below the name
  - ✓ for support, ✗ for not supported, "Beta" / "Partial" / "Enterprise add-on" / "Scale" for tier-gated features
- Tables typically have 8-15 rows
- After most tables: a "**Good to know**" callout (sometimes blockquoted with `>`, sometimes just bolded heading)

### "Good to know" callouts inventory (pillar)
The pillar has 8 such callouts. Several explicitly acknowledge where the competitor wins or where PostHog is weaker. Paraphrased by function:
- A gap acknowledgment: if a feature is missing today, it is likely already on the roadmap (acknowledges gaps)
- A free-tier callout stating the monthly free event allowance, linked to the pricing page (about PostHog)
- A pointer to the PostHog toolbar for viewing clickmaps on a live site (about PostHog feature)
- A note that the AI assistant can be used to query session recordings in natural language (about PostHog feature)
- A note that feature flags connect to the other PostHog tools (about PostHog feature)
- A note on how experiment results can be evaluated (about PostHog feature)
- A short explanation of what surveys are good for, such as satisfaction scores (educational)
- A short explanation of what web analytics covers at a high level (educational)
- A note that a Business Associate Agreement for HIPAA is available (about PostHog feature)

**The {{PRODUCT}} pattern for "Good to know" callouts should include MORE explicit competitor concession** (e.g. "Globex wins on setup speed for single-checklist teams..."). PostHog's are mostly self-promotional; {{PRODUCT}}'s mix self-promotion with honest concession to keep AEO/E-E-A-T strong. Concessions should draw on the "Not a fit" line of the Ideal customer profile and the Does NOT have section of product-context.md.

### Pricing section
- Three pricing tables for three usage scenarios
- Concrete dollar amounts at multiple volume tiers (1M, 3M, 5M, 10M, 15M, 20M)
- One concession blockquote acknowledging Mixpanel's anonymous-event pricing structure

### "When to choose PostHog vs Mixpanel" H2
- Two bullet points with **bolded answer** at the end
- Direct decision framework, no equivocation

### "Recommendations by team type" H2
- 5 use-case-specific subsections:
  1. For engineering-led product teams
  2. For product management teams
  3. For teams building AI products
  4. For privacy-conscious and regulated organizations
  5. For early-stage startups
- Each recommendation is a bolded product name (PostHog or Mixpanel) followed by 1-2 sentences
- **Notably, Mixpanel is recommended for product management teams.** PostHog explicitly concedes the use case where Mixpanel wins.

### Free usage table
- After Recommendations, a single table showing PostHog's free-tier limits across 7 product areas

### CTA banner
- A one-command install banner with an `npx` wizard code snippet
- Appears TWICE in the pillar (once mid-post, once near end)

### FAQ section ("Frequently asked questions" H2)
- **20 questions total**
- Each question is on its own line as a styled prompt (rendered as expandable accordions, but in markdown source they appear as plain text questions followed by answers)
- Answer style: starts with bolded **Yes,** / **No,** / **For most teams,** etc., then 2-4 sentences
- Internal links throughout answers (zero external links)
- Several FAQ answers have bulleted sub-lists (e.g. the "Mixpanel pricing" FAQ has a 3-item bulleted breakdown)
- One question explicitly uses a year (a "best all-in-one tools in {year}" style question). **This is the ONLY year reference in the entire post**

### Newsletter subscribe block
- Appears between FAQ and footer

### Bottom boilerplate blockquote
- Single italic blockquote at the very end (paraphrased structure): one sentence positioning PostHog as an all-in-one developer platform, then one long sentence listing every product (roughly 14), each inline-linked to its product page, closing with a clause on the outcome for the customer (debug faster, ship faster, keep all data in one stack).
- Every product name is inline-linked to its product page

### Community questions section
- "Ask a question" prompt for community engagement

### NO "Related reading" section
- The pillar does NOT have a separate Related Reading section
- Cross-links to sibling content are inline within the body and FAQ

---

## Listicle audit (Best Mixpanel Alternatives)

### Title and metadata
- **H1 title:** "The most popular Mixpanel alternatives & competitors, compared"
- **NO YEAR in title.** Repeat: NO YEAR.
- **Same authors and date as pillar** (co-launch pattern: pillar and listicle ship same day)
- **Tag:** "Comparisons"

### Opening structure
1. **NO QUICK-ANSWER BLOCK.** Direct opening.
2. **Three short opening paragraphs** (paraphrased structure):
   - Para 1: establishes the competitor's long track record in the category and how it has evolved. The competitor name links to PostHog's pillar, NOT to mixpanel.com.
   - Para 2: opens with a short pivot ("But Mixpanel isn't the only option"), then lists three reader motivations for looking elsewhere (all-in-one breadth, transparent pricing, a tool built for engineers) and says alternatives exist for each.
   - Para 3: one sentence stating what the guide compares and the two kinds of reader it serves (focused tool vs all-in-one platform).

### Main entries (4 entries)
Numbered H2 headers: "1. PostHog", "2. Google Analytics 4 (GA4)", "3. Amplitude", "4. Heap"

**For each entry, EXACTLY 6 H3 sub-sections in this order:**

1. **"What is [X]?"** (1-2 paragraph definition)
   - Often includes inline link to PostHog's comparison page for that competitor
   - For example, the GA4 entry opens with the tool name linked to PostHog's own PostHog vs GA4 comparison page
   - The competitor's name is linked, but ALWAYS to PostHog's own internal content

2. **"Key features"** (bulleted list of 5-8 features)
   - Each bullet bolded feature name + 1 sentence description
   - For PostHog entry: every feature inline-links to its product page
   - For competitor entries: features are NOT linked (no external links)

3. **"Who uses [X]?"** (3-4 bullet user types + customer name list)
   - "Typical [X] users are:" followed by 3-4 bullets
   - Then a customer-name sentence ("Customers include ..." with a short list of names). For the PostHog entry these link to internal customer case studies
   - For competitor entries: customer names are unlinked or just listed as text

4. **"How does [X] compare to Mixpanel?"**
   - Comparison table (same format as pillar tables: row = feature, columns = X and Mixpanel, values = ✓/✗/Partial)
   - **"Main differences between [X] and Mixpanel"** as 5-bullet list
   - **"Main similarities between [X] and Mixpanel"** as 5-bullet list
   - 1-2 paragraphs of synthesis prose AFTER the bullet lists

5. **"Why do companies use [X]?"**
   - For the PostHog entry, a framing line attributes the reasons to G2 reviews, linked to the G2 page
   - 3 numbered reasons (1, 2, 3)
   - Each reason has 2-3 sentences

6. **Bottom-line blockquote** in this exact format:
   ```
   > #### Bottom line
   >
   > [Verdict statement in 1-2 sentences naming who this is for and who should look elsewhere]
   ```

### NO external links to competitor websites
This is verified by careful inspection. Mixpanel mentions: 100+. Mixpanel.com links: ZERO. GA4 mentions: 30+. analytics.google.com links: ZERO. Amplitude mentions: 20+. amplitude.com links: ZERO. Heap mentions: 20+. heap.io links: ZERO.

The ONLY external links in the entire listicle go to:
- G2 reviews (third-party review aggregator), used as social proof
- Customer websites for case studies (these link to the customer site, e.g. NBCUniversal case study)
- The Heap acquisition link to contentsquare.com (one external news reference)

**Even those exceptions are sparse.** The default is internal-only.

### Honorable mentions
- H2 (no number, just "Honorable mentions", not a numbered entry)
- 5-bullet list, each bullet:
  - Bolded competitor name (NOT linked externally)
  - For some, the name links to PostHog's own alternatives post (e.g. "[Pendo](https://posthog.com/blog/best-pendo-alternatives)")
  - 1-2 sentence description
  - "Best for X." closer (sometimes)

### "Which [X] alternative should you choose?"
- 4-bullet decision framework
- Each bullet: question phrasing + bolded answer at the end

### "Is PostHog right for you?"
- "Here's the (short) sales pitch."
- Self-aware framing: a one-line admission of bias, followed by the claim that PostHog is the right replacement if the reader matches the conditions below
- 4-5 bullet conditions
- Closing CTA paragraph
- Repeats the wizard install snippet

### FAQ section
- 13 questions in the listicle (vs 20 in the pillar)
- Same answer format as pillar: bolded **Yes,** / **No,** / etc.
- Internal-only links

### Bottom boilerplate blockquote
- Same boilerplate as pillar's bottom blockquote (re-used verbatim across all PostHog comparison-cluster posts)

### NO "Related reading" section
- Same as pillar: cross-links are inline in FAQ answers, not a separate section

---

## What this means for {{PRODUCT}}

### Adopt verbatim
1. NO quick-answer block. Direct intro.
2. NO year in any H1, H2, H3, H4.
3. NO external links to competitor websites. Ever.
4. Numbered definition pair at top of pillar.
5. 8-10 capability comparison H3 sections in pillar with tables and "Good to know" callouts.
6. 18-22 FAQ questions in pillar.
7. 12-15 FAQ questions in listicle.
8. 4 main entries in listicle (not 5 or 6).
9. Per-entry 6-section template in listicle.
10. Bottom-line blockquote per entry in listicle.
11. Bottom boilerplate blockquote (italic, all products inline-linked) at end of every cluster post.
12. NO "Related reading" section in pillar or listicle (it's allowed in the migration guide, but not the others).

### Adapt for {{PRODUCT}}
1. Tone is professional, not self-deprecating, unless product-context.md says otherwise. No mockery of competitors.
2. "Good to know" callouts must include MORE explicit competitor concessions ({{PRODUCT}}'s AEO advantage is honesty).
3. {{PRODUCT}}'s H1 disambiguation: follow the first-mention rule in product-context.md. Mention former names only if product-context.md lists them and the post is specifically about migration.
4. {{PRODUCT}}'s customer list: only the customer logos listed under Verified proof points in product-context.md. Never older or unlisted names.
5. {{PRODUCT}}'s onboarding language: describe setup exactly as product-context.md documents it (see Rule 7 in SKILL.md). NEVER generic "configure modules" or "activate features in admin" phrasing unless that is the documented flow.
6. {{PRODUCT}}'s voice rules: NO em dashes (PostHog uses them; the house style default does not), no ampersands in body copy. Add any rules from the Voice section of product-context.md.

### Reject any draft that violates the above
The cluster-writer skill should refuse to ship a post that fails any of the above rules. Log the violation, surface to operator, do not auto-fix.
