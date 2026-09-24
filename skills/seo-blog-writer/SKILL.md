---
name: seo-blog-writer
description: "Produces top 1% on-page SEO blog posts for SaaS and B2B brands using the hub-and-spoke topic cluster model. Use this skill whenever the user asks to write a blog post, comparison post, alternatives post, listicle, product review, or any long-form content that needs to rank on Google. Also trigger on: 'write a blog', 'SEO blog', 'comparison post', 'alternatives post', 'vs post', 'best X tools', 'X vs Y', 'competitor comparison', 'topic cluster', 'pillar page', 'hub and spoke content', 'rank for keyword', 'on-page SEO', 'write content that ranks', 'blog post outline', 'SEO outline', 'content brief', or any request to produce a blog post optimized for organic search. Trigger aggressively -- if there is any mention of blog writing, SEO content, comparison content, or ranking for keywords, use this skill."
---

# SEO Blog Writer -- Top 1% On-Page SEO for SaaS and B2B

## Overview

This skill produces blog posts engineered to rank in the top 1% of organic search results. It is based on a reverse-engineered analysis of elite SaaS SEO operators (PostHog, HubSpot, Ahrefs, Semrush) and codifies their keyword strategy, content structure, interlinking architecture, EEAT signals, FAQ engineering, and topic cluster design into a repeatable production system.

This skill does NOT reference or depend on any other skills. It is self-contained.

If `product-context.md` exists in the working folder (see the cluster-planner template), read it first and only claim features it documents.

---

## Step 0: Determine the post type

Before writing anything, classify the request into one of these post types. Each type has a different structure, keyword strategy, and funnel position.

| Post type | Funnel position | Primary keyword pattern | Example |
|---|---|---|---|
| Head-to-head comparison | BOFU | "[Brand] vs [Competitor]" | "Acme vs Globex" |
| Alternatives listicle | BOFU | "Best [Competitor] alternatives" | "Best Globex alternatives" |
| Category listicle | MOFU | "Best [category] tools for [audience]" | "Best project management tools for agencies" |
| How-to / tutorial | MOFU | "How to [action] with [tool]" | "How to build a project roadmap" |
| Thought leadership | TOFU | "[Trend/concept] for [audience]" | "AI in project management for agencies" |
| Case study / social proof | BOFU | "[Customer] switched from [X] to [Y]" | "How [customer] cut reporting time with Acme" |

Once classified, follow the corresponding structure template in Step 3.

---

## Step 1: Keyword architecture

Every blog post targets a three-tier keyword structure. Define all three tiers BEFORE writing.

### Primary keyword (1 keyword)
- The exact phrase the post must rank for
- Must appear in: H1 title, URL slug, meta title, first 100 words, at least 2 H2 subheadings
- Density: 0.8% to 1.5% of total word count (natural placement, never forced)

### Secondary keywords (3 to 6 keywords)
- Semantic variations and related search queries
- Must appear in: H2 or H3 subheadings, body paragraphs, image alt text, comparison table headers
- Examples: if primary is "Acme vs Globex", secondaries include "Globex alternative", "project management software comparison", "Acme pricing vs Globex pricing"

### Tertiary keywords (6 to 15 keywords)
- Long-tail queries captured by FAQ section and deep feature discussions
- Must appear in: FAQ questions (written as exact user queries), feature comparison rows, "Good to know" callouts
- Examples: "does Globex have a mobile app", "Acme free tier", "migrate from Globex"

### On-page keyword placement tactics

These are the specific techniques for weaving keywords into headlines, subheadings, body copy, tables, FAQs, and callouts so that placement reads naturally to humans while signaling relevance to search engines.

#### Title tag and H1 headline

The title tag (what appears in the SERP) and the H1 (what appears on the page) can differ. Use this to hit two keyword phrasings at once.

- **Title tag pattern**: "[Primary keyword] [modifier]". Keep the primary keyword at the front. Add a descriptive modifier after it that includes a secondary keyword or adds specificity.
  - Example: "PostHog vs Mixpanel" becomes the title tag "PostHog vs Mixpanel in-depth tool comparison" -- primary keyword leads, "tool comparison" is a secondary keyword.
  - Example: "Best Mixpanel alternatives" becomes the title tag "The most popular Mixpanel alternatives & competitors, compared" -- primary keyword "Mixpanel alternatives" is present, and "competitors" and "compared" are secondary keywords that capture variant queries.
- **H1 pattern**: Match the title tag exactly, or use a close variant. The H1 can be slightly longer and more natural-reading than the title tag.
- **Never stuff**: the primary keyword appears once in the H1, not twice. No keyword repetition within the same headline.

#### URL slug

- Primary keyword only, hyphenated, stripped of stop words and modifiers.
  - Title tag "PostHog vs Mixpanel in-depth tool comparison" becomes slug `/blog/posthog-vs-mixpanel`
  - Title tag "The most popular Mixpanel alternatives & competitors, compared" becomes slug `/blog/best-mixpanel-alternatives`
- The slug is shorter and more generic than the title tag. This lets the title tag change over time (for freshness) without breaking the URL.

#### Meta description

- Primary keyword + one secondary keyword + a benefit statement or CTA, under 155 characters.
- Example: "Compare PostHog vs Mixpanel on product analytics, session replay, feature flags, and pricing. Find the right product analytics tool for your team."
- The primary keyword ("PostHog vs Mixpanel") and secondary ("product analytics tool") both appear. The benefit is clear ("find the right tool").

#### First paragraph keyword integration

The primary keyword must appear in the first 100 words, but it must read as a natural opening, not a keyword dump. Use one of these patterns:

- **Competitor-first bridge**: Name the competitor, acknowledge its strengths in one sentence, then pivot to alternatives. This places the competitor's brand name (which is part of the primary keyword) naturally at the very start.
  - Example: "[Mixpanel] has been a go-to product analytics platform for over a decade, and it has evolved significantly. But [Mixpanel] is not the only option. Whether you are looking for [secondary keyword: all-in-one platform], [secondary keyword: transparent pricing], or [secondary keyword: tool built for engineering teams], there are alternatives worth considering."
  - This opening contains the primary keyword "Mixpanel alternatives" implicitly through "Mixpanel" + "alternatives" appearing in close proximity. It also seeds three secondary keywords in the very first paragraph.

- **Direct comparison bridge**: For head-to-head posts, name both brands in the first sentence and describe what they share, then state what the post will cover.
  - Example: "[PostHog] and [Mixpanel] are both product analytics platforms helping teams understand user behavior. Both have expanded beyond core analytics, but their approaches differ."
  - The primary keyword "PostHog vs Mixpanel" is embedded naturally through both brand names appearing together. "Product analytics" is a secondary keyword placed in the same sentence.

#### H2 and H3 subheading keyword strategies

Subheadings are the single highest-impact on-page SEO element after the H1. These are the specific phrasing patterns to use:

**Pattern 1: Feature category as H3 subheading (comparison posts)**
Use the exact feature category name as the H3. This turns every comparison section into a keyword-rich subheading that can independently rank for "[feature] comparison" queries.
- H2: "Comparing [Brand] and [Competitor]"
- H3: "Product analytics" / "Session replay" / "Feature flags" / "Experiments" / "Surveys" / "Price comparison" / "Data integrations" / "Security and compliance"
- Each of these H3s is a secondary or tertiary keyword on its own. "Product analytics" ranks for "[Brand] product analytics". "Price comparison" ranks for "[Brand] vs [Competitor] pricing".

**Pattern 2: "How does [X] compare to [Competitor]?" as H3 (alternatives posts)**
For alternatives listicles, each competitor section uses a question-format H3 that mirrors a real search query.
- H3: "How does PostHog compare to Mixpanel?" / "How does GA4 compare to Mixpanel?" / "How does Amplitude compare to Mixpanel?"
- This phrasing naturally includes both the competitor name and the "compare" keyword, capturing "[X] vs Mixpanel" and "compare [X] to Mixpanel" queries.

**Pattern 3: "Why do companies use [X]?" as H3 (alternatives posts)**
This phrasing captures "[X] reviews" and "why use [X]" search queries.
- H3: "Why do companies use PostHog?" / "Why do companies use GA4?"

**Pattern 4: Benefit-first H2 for value proposition sections**
When the section is about the brand's differentiators, lead with the benefit, not the feature.
- H2: "How is [Brand] different?" (not "Features of [Brand]")
- H3: "We are an all-in-one platform" / "We build for developers" / "We promise transparent and cheap pricing"
- These H3s contain secondary keywords ("all-in-one platform", "transparent pricing") naturally embedded in benefit statements.

**Pattern 5: Decision-matrix H2 at the end**
A summary section with a keyword-rich heading that captures "which [X] should I choose" queries.
- H2: "Which Mixpanel alternative should you choose?"
- H2: "Is [Brand] right for you?"

#### Comparison table keyword loading

Comparison tables are keyword-dense by design. Each row is a keyword opportunity:

- **Feature name column**: use the exact industry-standard feature name as it would be searched. "Session replay" not "User recording". "Feature flags" not "Toggle management". "A/B testing" not "Split testing" (unless "split testing" is also a target keyword, in which case include both as separate rows).
- **Feature description column**: write a one-sentence description that includes a long-tail keyword variant. Example: "Feature flags" row description reads "Control feature access with precision and safely roll out changes" -- this captures "feature flags rollout" and "control feature access" as tertiary keywords.
- **Table header**: include both brand names. The table header should read "[Brand] | [Competitor]" so the brand-vs-competitor keyword pair appears in a structured data context.

#### Bridge sentences between sections

Use a bridge sentence before each new comparison section that naturally includes a secondary keyword and creates a contextual link to another blog post.

- Example before the Website Analytics section: "[Brand] is also a powerful [alternative to Google Analytics](/blog/ga4-alternatives) that bridges the gap between lightweight tools like [Plausible](/blog/best-plausible-alternatives) and expensive enterprise platforms like [Adobe Analytics](/blog/best-adobe-analytics-alternatives)."
- This single sentence contains three secondary keywords ("alternative to Google Analytics", "Plausible", "Adobe Analytics") and three internal links, all reading as natural editorial context.

- Example before the main comparison section: "As an all-in-one platform, [Brand] is not just [an alternative to Mixpanel](/blog/best-mixpanel-alternatives), it can also replace tools like [Hotjar](/blog/best-hotjar-alternatives) for session replay and surveys, and [LaunchDarkly](/blog/best-launchdarkly-alternatives) for experiments and feature flags."
- This sentence places the primary keyword variant ("alternative to Mixpanel"), two competitor secondary keywords ("Hotjar", "LaunchDarkly"), and three internal links within a single natural sentence.

#### "Good to know" callout keyword placement

Callout boxes after comparison table sections are keyword opportunities disguised as helpful tips:

- Place one secondary or tertiary keyword naturally inside each callout.
- Example: "Our [generous free tier](/pricing) means every [Brand] customer gets 1 million analytics events for free every single month." -- "free tier" and "analytics events" are tertiary keywords, and /pricing gets an internal link.
- Example: "You can use [Brand] AI to chat with your recordings using natural language." -- "chat with recordings" and "natural language" are tertiary keywords capturing conversational search queries.

#### FAQ question keyword phrasing

FAQ questions must be written as exact search queries, not as generic section headers:

- **Yes/no migration question**: "Can I migrate my data from [Competitor] to [Brand]?" -- captures "migrate from [Competitor]" and "[Competitor] to [Brand] migration"
- **Replacement question**: "Can [Brand] replace [Other tool]?" -- captures "[Brand] vs [Other tool]" and "[Brand] alternative to [Other tool]"
- **Feature gap question**: "Does [Competitor] have [feature]?" -- captures "does [Competitor] have [feature]" exactly as searched. Write one FAQ per major feature the competitor lacks.
- **Pricing question**: "What is included in [Brand]'s free tier?" -- captures "[Brand] free tier" and "[Brand] pricing"
- **Best-for questions**: "Which alternative is best for [audience]?" -- captures "best [Competitor] alternative for [audience]"
- **Integration question**: "Can I use [Brand] with a CDP? (Segment, Rudderstack, etc.)" -- captures "[Brand] Segment integration" and "[Brand] CDP" as tertiary keywords. The parenthetical naturally includes tool names people search for.

#### Image alt text keyword integration

Every image alt text follows the pattern: [what the image shows] + [secondary keyword where natural].

- Example: alt="PostHog product analytics dashboard showing funnels and retention" -- describes the image AND includes "product analytics dashboard", "funnels", and "retention" as secondary keywords.
- Example: alt="Comparison of PostHog and Mixpanel pricing" -- describes the image AND includes the primary keyword "PostHog and Mixpanel" plus "pricing".

#### Keyword repetition through structured repetition

Both brand names and the primary keyword get naturally repeated through the post's structure without ever appearing forced:

- The comparison table header repeats "[Brand] | [Competitor]" in every table section (6 to 10 times across the post)
- Each "Good to know" callout starts with "Our..." or "[Brand]..." reinforcing the brand name
- Each FAQ question includes "[Brand]" or "[Competitor]" by name
- The footer CTA block restates the full product description with every feature name linked

This means the primary keyword phrase (both brand names together) appears 15 to 25 times across a 5,000-word comparison post, but distributed across tables, headings, FAQs, and callouts so it never reads as repetitive in any single section.

---

## Step 2: Content specifications

### Word count targets by post type
- Head-to-head comparison: 4,000 to 6,000 words
- Alternatives listicle: 5,500 to 8,000 words
- Category listicle: 3,500 to 5,500 words
- How-to / tutorial: 2,500 to 4,000 words
- Thought leadership: 2,000 to 3,500 words
- Case study: 1,500 to 2,500 words

These ranges include comparison tables, FAQs, and callouts. Do not pad. Every word must earn its place.

### Tonality rules
- First person plural ("we", "our") when writing from the brand's perspective
- Direct, confident, opinionated -- not hedging or corporate-safe
- Developer-native vocabulary when the audience is technical; benefit-first vocabulary when the audience is non-technical
- Self-aware about bias: acknowledge it openly rather than pretending objectivity (e.g., "We are biased, obviously, but we think...")
- Dry humor is welcome; forced humor is not
- Zero filler phrases: never use "in today's fast-paced world", "it goes without saying", "without further ado", "in conclusion"
- No em dashes. No ampersands. American English throughout.

### Word choice rules
- Use the precise technical term, not a simplified version (e.g., "autocapture" not "automatic event tracking")
- Feature names are always hyperlinked to their product page on first mention
- Competitor names are always hyperlinked to the relevant comparison or alternatives post on first mention
- Never use "click here" or "learn more" as anchor text. Anchors must be descriptive and keyword-rich.
- Avoid "best-in-class", "cutting-edge", "robust", "seamless", "leverage", "utilize"

### Readability rules
- Paragraphs: 2 to 4 sentences maximum
- Sentences: 15 to 25 words average. Mix short punchy sentences with longer explanatory ones.
- Comparison tables: use checkmarks and X marks for quick scanning. Tables are the dominant content format for comparison posts.
- Callout boxes: use "Good to know" or "Pro tip" callouts to break up table-heavy sections with conversational context
- Flesch-Kincaid target: grade 9 to 12 depending on audience technical level
- Bullet points: only for feature lists and key differentiators, never for general prose
- Bold text: use sparingly for feature names in lists, never for emphasis in running prose

---

## Step 3: Structure templates

### Template A: Head-to-head comparison post

```
# [H1: Brand vs Competitor -- descriptive subtitle]

Author byline + date + category tag

## Table of contents (auto-generated, persistent sidebar)

## How is [Brand] different? (3 numbered value propositions)
  ### 1. [Core differentiator 1]
  ### 2. [Core differentiator 2]
  ### 3. [Core differentiator 3]

## Comparing [Brand] and [Competitor]

  ### Platform overview (feature comparison table -- full product suite)

  > Good to know: [Contextual tip about roadmap, free tier, or unique capability]

  ### [Feature category 1] (detailed comparison table)
  ### [Feature category 2] (detailed comparison table)
  ### [Feature category 3] (detailed comparison table)
  ... repeat for each major feature category (aim for 6 to 10 categories)

  > Good to know: [Tip after every 2-3 table sections]

  ### Price comparison (pricing tables with multiple scenarios)
  ### Data integrations (comparison table)
  ### Security and compliance (comparison table)

## Frequently asked questions (5 to 8 FAQ items, accordion format)

## [Footer CTA block with product description and links]
```

### Template B: Alternatives listicle post

```
# [H1: The best/most popular [Competitor] alternatives and competitors, compared]

Author byline(s) + date + category tag

## Table of contents

[Intro paragraph: 2-3 sentences on why people look for alternatives. Mention the competitor's strengths fairly, then state what gaps exist.]

## 1. [Top alternative -- your brand]
  ### What is [Brand]?
  ### Key features (bullet list, 6-8 features with one-line descriptions)
  ### Who uses [Brand]? (3-4 bullet persona descriptions + named customers)
  ### How does [Brand] compare to [Competitor]? (comparison table)
  Main differences (expandable/collapsible list, 4-5 items)
  Main similarities (expandable/collapsible list, 4-5 items)
  [1-2 paragraphs of analysis]
  ### Why do companies use [Brand]? (cite G2 or Capterra reviews, 3 reasons)
  > Bottom line: [2-3 sentence verdict in a callout box]

## 2. [Alternative 2]
  [Same sub-structure as above]

## 3. [Alternative 3]
  [Same sub-structure as above]

## 4. [Alternative 4]
  [Same sub-structure as above]

[Honorable mentions section: 4-6 additional tools with 2-3 sentence descriptions each]

## Which [Competitor] alternative should you choose? (decision matrix, 4-5 bullet verdicts)

## Is [Brand] right for you? (soft CTA section)

## Frequently asked questions (10 to 15 FAQ items)

## [Footer CTA block]
```

### Template C: Category listicle post

```
# [H1: The [N] best [category] tools for [audience]]

Author byline + date + category tag

## Table of contents

[Intro: what this category is, who needs it, what to look for]

## 1. [Tool 1 -- your brand]
  [Screenshot]
  ### Key features
  ### Best for
  ### Pricing
  > Bottom line

## 2-N. [Remaining tools, same structure]

## How to choose [category] (buying guide section, 3-5 criteria)

## Frequently asked questions (8-12 FAQ items)
```

### Template D: How-to / tutorial post

```
# [H1: How to [action] with [tool/method]]

Author byline + date + category tag

## Table of contents

[Intro: what the reader will learn, why it matters, what they need before starting]

## Prerequisites / what you will need

## Step 1: [Action verb + specific outcome]
  [2-4 paragraphs with screenshots or code blocks]

## Step 2: [Action verb + specific outcome]
  [2-4 paragraphs with screenshots or code blocks]

... repeat for each step

## [Optional: Advanced tips or variations]

## Frequently asked questions (5-8 FAQ items)
```

---

## Step 4: EEAT signals

Every blog post must include these Experience, Expertise, Authority, and Trust signals:

### Experience
- Include hyper-specific feature knowledge that can only come from hands-on usage (e.g., knowing a competitor's free tier limits, knowing which features are Enterprise-only)
- Reference real pricing with actual dollar amounts, not vague "affordable" or "competitive"
- Mention specific SDK support, API endpoints, or technical implementation details where relevant

### Expertise
- Author attribution with link to author profile page
- Technical vocabulary matching the audience's level -- do not simplify for imagined beginners
- Link to the brand's own documentation, tutorials, and API docs as evidence of deep knowledge

### Authority
- Cite G2, Capterra, or TrustRadius reviews with direct attributions
- Reference named customers (with permission) and link to case studies
- Link to the brand's GitHub, public roadmap, or changelog as transparency signals
- Include pricing tables that can be independently verified on the pricing page

### Trust
- Transparent pricing: never gate pricing behind "contact sales" in the blog post
- Prominent free tier information
- Openly acknowledge bias: "We are biased, obviously, but..."
- Security and compliance section with specific certifications (SOC 2, GDPR, HIPAA) where relevant
- Include a "Consider keeping [Competitor] if..." section in comparison posts -- showing fairness

### Commonly missed EEAT signals (add these to stand out)
- Structured author bio with credentials at the top or bottom of the post
- "Reviewed by" or "Fact-checked by" attribution
- Visible "Last updated: [date]" badge prominently displayed
- Customer testimonial quotes embedded within comparison sections, not just on a separate page
- Competitor screenshots alongside your own product screenshots

---

## Step 5: Internal linking and topic cluster architecture

### The hub-and-spoke model

Every blog post exists within a topic cluster. Before writing, define the cluster:

1. **Hub page**: the broadest keyword in the cluster (e.g., "Best Globex alternatives"). This is the alternatives listicle or category pillar page.
2. **Spoke pages**: each specific head-to-head comparison (e.g., "Acme vs Globex", "Acme vs Initech"). Each spoke links to and from the hub.
3. **Supporting content**: tutorials, how-to guides, case studies, migration guides that reference the competitor or category. Each links to the nearest spoke or hub.

### Internal linking rules

- **Minimum 30 internal links per comparison or alternatives post, 15 for other post types**
- **Bidirectional linking between hub and spokes**: the hub links to every spoke, and every spoke links back to the hub
- **Cross-spoke linking**: each spoke should link to at least 2 sibling spokes
- **Product page links**: every feature name mentioned in a comparison table must hyperlink to the brand's product page for that feature
- **Documentation links**: link to migration guides, API docs, setup tutorials from within the post body and FAQ section
- **Conversion page links**: include links to /pricing, /startups or equivalent, and /signup within "Good to know" callouts and the FAQ section
- **Anchor text**: always descriptive and keyword-rich. Never "click here", "learn more", or "read this"
- **External links**: minimal. One or two for social proof citations (G2, Capterra). Keep link equity internal.

### Link equity flow design

```
[Alternatives hub] <--bidirectional--> [Head-to-head comparison spoke]
        |                                        |
        v                                        v
[Sibling alternatives hubs]          [Product pages: /features, /pricing...]
        |                                        |
        v                                        v
[Category listicles]                 [Docs: /migrate, /api, /tutorials]
                                                 |
                                                 v
                                     [Conversion: /pricing, /signup, /free-trial]
```

### Topic cluster planning table

Before writing any post, fill in this table for the entire cluster:

| Post title | Primary keyword | Intent type | Funnel stage | Links TO this post from | Links FROM this post to |
|---|---|---|---|---|---|
| [Fill per post] | [Fill] | Comparison / Listicle / Tutorial / Case study | TOFU / MOFU / BOFU | [List source posts] | [List target posts] |

---

## Step 6: FAQ engineering

The FAQ section is not an afterthought. It is a deliberate long-tail keyword capture mechanism designed to win Featured Snippets and People Also Ask boxes.

### FAQ rules

- **Minimum 5 FAQs for comparison posts, 10 for alternatives listicles, 5 for all other types**
- **Questions must be written as exact Google search queries**: "Does [Competitor] have error tracking?" not "What about error tracking?"
- **Answer length varies by complexity**: simple yes/no questions get 1-2 sentences + a link. Complex questions get 2-3 paragraphs.
- **Every FAQ answer must contain at least one internal link**
- **FAQ categories to always include for comparison/alternatives posts**:
  - Migration question: "Can I migrate from [Competitor] to [Brand]?"
  - Free tier question: "What is included in [Brand]'s free tier?"
  - Feature gap questions: "Does [Competitor] have [feature Brand has but Competitor lacks]?" (one FAQ per missing feature)
  - Audience-specific questions: "Which alternative is best for [audience segment]?" (one per major segment)
  - Open source question (if applicable): "Which [Competitor] alternatives are open source?"
  - Data/integration question: "Can I use [Competitor] with my data warehouse / CRM / tools?"

### FAQ tonality
- Direct, informative, no hedging
- Match the technical level of the rest of the post
- Bold the brand name in answers where it is the recommended solution
- Lead with the direct answer, then provide context
- Keep under 200 words per answer for simple questions, under 350 for complex ones

---

## Step 7: Schema markup recommendations

Include these in the production brief or as a comment block at the bottom of every post:

- **Article schema**: type, headline, author (with name and URL), datePublished, dateModified, publisher, image
- **FAQPage schema**: every FAQ question-answer pair marked up for rich snippet eligibility
- **BreadcrumbList schema**: Blog > Category > Post title
- **Review schema** (for comparison posts with a verdict/score)
- **Author schema**: link to author's profile page with sameAs links to LinkedIn/Twitter

---

## Step 8: Pre-publish checklist

Before finalizing any blog post, verify every item:

### Keywords and on-page placement
- [ ] Title tag: primary keyword front-loaded within first 60 characters, secondary keyword in modifier
- [ ] H1 matches or closely variants the title tag, primary keyword appears once (never twice)
- [ ] URL slug: primary keyword only, hyphenated, no stop words or modifiers
- [ ] Meta description: primary keyword + secondary keyword + benefit/CTA, under 155 characters
- [ ] First paragraph: primary keyword within first 100 words using competitor-first bridge or direct comparison bridge pattern
- [ ] First paragraph also seeds 2-3 secondary keywords naturally
- [ ] H2/H3 subheadings: feature category names used as exact H3s (comparison posts)
- [ ] H2/H3 subheadings: "How does [X] compare to [Competitor]?" pattern used (alternatives posts)
- [ ] At least 2 of all H2s contain the primary or a secondary keyword
- [ ] Comparison table headers repeat "[Brand] | [Competitor]" in every table section
- [ ] Feature description column in tables includes long-tail keyword variants
- [ ] Bridge sentences before sections include secondary keywords + internal links to sibling posts
- [ ] "Good to know" callouts each contain at least one secondary or tertiary keyword
- [ ] FAQ questions are phrased as exact Google search queries (not generic headers)
- [ ] FAQ questions include brand/competitor names explicitly
- [ ] Image alt text follows "[image description] + [secondary keyword]" pattern
- [ ] Primary keyword appears 15-25 times across the full post, distributed across tables, headings, FAQs, callouts (never clustered)
- [ ] No keyword stuffing: no section has the primary keyword more than twice

### Structure
- [ ] Word count meets target range for the post type
- [ ] Correct template used for the post type
- [ ] Table of contents present
- [ ] "Good to know" callouts after every 2-3 table sections (comparison posts)
- [ ] Comparison tables use checkmarks/X marks, not prose descriptions

### Internal linking
- [ ] Minimum link count met (30 for comparison/alternatives, 15 for others)
- [ ] Bidirectional link to hub page exists
- [ ] Links to at least 2 sibling spoke posts exist
- [ ] Every feature name links to its product page
- [ ] No "click here" or "learn more" anchor text anywhere

### FAQs
- [ ] Minimum FAQ count met for the post type
- [ ] Every FAQ question is phrased as a real Google search query
- [ ] Every FAQ answer contains at least one internal link
- [ ] Migration, free tier, and feature gap FAQs are included

### EEAT
- [ ] Author byline with profile link present
- [ ] "Last updated" date visible
- [ ] Pricing is specific (real dollar amounts)
- [ ] At least one customer name/case study referenced
- [ ] G2/Capterra review citation included (comparison/alternatives posts)
- [ ] Bias openly acknowledged in comparison posts

### Copy quality
- [ ] Meta description under 155 characters with primary + secondary keyword
- [ ] No em dashes, no ampersands, American English throughout
- [ ] No banned filler phrases or adjectives
- [ ] Paragraphs 2-4 sentences maximum
- [ ] No hedging language ("might", "could potentially", "it is possible that")

### Technical SEO
- [ ] Schema markup recommendations documented
- [ ] Image alt text includes secondary keywords
- [ ] URL slug is clean, hyphenated, no stop words

---

## Step 9: Output format

When the user asks you to write a blog post using this skill, deliver:

### If the user asks for planning first, deliver a content brief:
1. Post type classification
2. Three-tier keyword architecture table (primary, secondary, tertiary)
3. Topic cluster table showing where this post fits and all interlinks
4. Recommended internal links (to and from)
5. FAQ question list (questions only, for approval before writing)
6. Suggested meta title and meta description

### If the user asks for the full post, deliver:
1. Meta title and meta description at the top
2. Complete post in markdown following the appropriate structure template
3. All internal links marked as `[anchor text](URL)` with real or placeholder URLs
4. Comparison tables in markdown table format with checkmarks/X marks
5. FAQ section with full answers and internal links
6. Schema markup recommendations as a comment block at the bottom
7. Pre-publish checklist with all items checked/flagged

### If the user asks for the broader cluster strategy:
1. Full cluster table with all posts, keywords, intent types, funnel stages, and interlink plans
2. Visual cluster diagram if requested
3. Content calendar recommendation (which posts to publish first for maximum link equity)

---

## Reference: Banned phrases and words

Never use any of these in blog content produced by this skill:

### Banned filler phrases
- "In today's fast-paced world"
- "Without further ado"
- "In conclusion"
- "It goes without saying"
- "At the end of the day"
- "When it comes to"
- "In order to" (use "to")
- "Due to the fact that" (use "because")
- "At this point in time" (use "now")
- "For the purpose of" (use "to" or "for")

### Banned adjectives and verbs
- "Game-changer" / "Game-changing"
- "Best-in-class"
- "Cutting-edge"
- "Robust"
- "Seamless" / "Seamlessly"
- "Leverage" (as a verb)
- "Utilize" (use "use")
- "Streamline"
- "Empower"
- "Synergy" / "Synergize"
- "Holistic"
- "Innovative" / "Innovation" (unless citing a specific innovation)
- "Revolutionize"
- "Disrupt" / "Disruptive" (unless specifically about market disruption theory)
- "World-class"
- "State-of-the-art"
- "Next-generation"
- "Comprehensive" (use specific descriptions instead)
- "Powerful" (describe what makes it powerful instead)

### Banned anchor text
- "Click here"
- "Learn more"
- "Read this"
- "Read more"
- "Check it out"
- "See here"
- "This article"
- "This link"
