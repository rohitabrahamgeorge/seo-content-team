---
name: cluster-writer
description: Writes publish-ready posts for a {{PRODUCT}} SEO content cluster following the strict PostHog hub-and-spoke template. Use this skill whenever the orchestrator hands off a post type (pillar, alternatives listicle, migration guide, narrative, pricing explainer, 3-way comparison, B2B review, or category explainer) for a {{PRODUCT}}-vs-competitor cluster. Trigger on phrases like "write the pillar", "write the listicle", "write the migration guide", "write post 4 of the cluster", "draft the comparison post", or any handoff from cluster-orchestrator with a confirmed plan and SERP analysis. This skill codifies the strict line-by-line PostHog rules verified by direct fetches of posthog.com/blog/posthog-vs-mixpanel and posthog.com/blog/best-mixpanel-alternatives. Do not invent features, do not add quick-answer blocks, do not use years in headlines, do not link to external competitor sites. Every product claim must be documented in product-context.md.
---

# Cluster Writer

This skill writes {{PRODUCT}} competitor-cluster posts that follow PostHog's hub-and-spoke pattern verbatim. The patterns below are observed line-by-line from PostHog's actual posts, not guessed.

## When to use this skill

Trigger when the orchestrator (or a user) asks for a specific post in a {{PRODUCT}} cluster:
- "Write the pillar for {{PRODUCT}} vs [competitor]"
- "Write the alternatives listicle"
- "Write the migration guide"
- "Draft post 4"

Required inputs:
- Cluster plan (from cluster-planner)
- SERP analysis with PAA questions and competitor positioning
- `product-context.md` (the filled product context file at the working-folder root, next to `clusters/`)
- PostHog template audit (in `references/posthog-template-audit.md`)
- Target post type (pillar / listicle / migration / narrative / pricing / 3-way / review / category)

If any of these are missing, halt and ask the orchestrator to provide them. Do not proceed with assumptions.

## Required reference reads at the start of every cluster run

Before writing the first word, read these in order:

1. `references/posthog-template-audit.md` (this skill folder). Line-by-line audit of PostHog's actual pillar and listicle, the source of truth for the template
2. `product-context.md` (working-folder root, next to `clusters/`). Verified product inventory from {{WEBSITE}} and {{DOCS_URL}}, the source of truth for what {{PRODUCT}} can actually do. Anything not documented there does not exist, and the writer must refuse to claim it
3. https://posthog.com/blog/posthog-vs-mixpanel. Re-fetch the live pillar to catch any drift
4. https://posthog.com/blog/best-mixpanel-alternatives. Re-fetch the live listicle
5. {{WEBSITE}}. Verify the live product positioning hasn't changed since product-context.md was last checked
6. {{DOCS_URL}}, the product's help center or knowledge base (when relevant for migration/technical content)

**Docs footprint rule:** {{DOCS_URL}} is authoritative for technical and migration facts (setup steps, import formats, limits). It is not a source of blog prose. Use it to verify, never to copy wording or structure into a post.

If `product-context.md` or the references/ files don't exist for the cluster, halt and ask the orchestrator to set them up.

## The 14 strict rules (verified line-by-line from PostHog)

### Rule 1: NO quick-answer block at the top of pillars

**Scope:** Pillar posts only. (Listicles have a different rule. See Rule 14.)

PostHog pillars start with a direct intro paragraph followed by a numbered definition pair. They do NOT have a "Quick answer:" callout.

❌ Wrong:
```
**Quick answer:** Acme and Globex are not direct competitors...
```

✓ Right:
```
Acme and [Globex](/blog/best-globex-alternatives) are both platforms that help teams onboard new customers. Both let you build guided checklists, send in-app messages, and track activation. Their approaches and ideal customers differ.

1. **Globex** is a self-serve onboarding tool built for...
2. **Acme** is a customer onboarding platform for B2B software teams. It combines...
```

### Rule 2: NO year in any headline

PostHog never puts "in 2026" or year qualifiers in titles. The date appears only in metadata.

❌ Wrong: `# Best Globex alternatives in 2026`
✓ Right: `# The most popular Globex alternatives & competitors, compared`

This applies to H1, H2, H3, H4. No year anywhere in headings.

### Rule 3: NO external links to competitor websites

PostHog NEVER links to mixpanel.com, amplitude.com, heap.io, or any competitor site. Every "Mixpanel" mention is either unlinked or links to PostHog's own internal alternatives/comparison page.

❌ Wrong: `[Globex](https://globex.example) is a self-serve onboarding tool`
✓ Right: `Globex is a self-serve onboarding tool` (no link)

This is the closed-garden rule. Link equity stays inside the {{PRODUCT}} content network.

### Rule 4: NO em dashes anywhere

House style default (keep it unless the Voice section of product-context.md says otherwise). Check the brand voice skill named in product-context.md, if one is installed, otherwise the Voice section of product-context.md, for any additional voice rules. Replace em dashes with periods (followed by capital letter) or commas (followed by lowercase) based on context. Run a sed/python pass before saving the post.

❌ Wrong: `Acme — Guided onboarding as an activation lever`
✓ Right: `Acme. Guided onboarding as an activation lever`

### Rule 5: NO ampersands in body copy

Headlines and titles can use `&` if matching PostHog convention (e.g. "alternatives & competitors, compared"). Body copy uses "and".

### Rule 6: Verified product context only. No invented features

Before writing anything about {{PRODUCT}}'s capabilities, read `product-context.md`. Cross-check every feature claim against its Verified capabilities, Integrations, Pricing and Verified proof points sections. If a claim isn't documented in product-context.md, the product does not have it: do not write it, and refuse if asked to. Anything listed under Does NOT have must never be claimed.

DO NOT write:
- Concepts the product doesn't have (anything under Does NOT have, or any setup concept such as "integration credentials" that is not described in product-context.md)
- "Native [CRM] integration apps" unless the Integrations section lists a native app (a lead export or webhook is not a native integration; describe it exactly as documented)
- Certifications (SOC 2, ISO 27001, HIPAA and similar) unless listed under Verified capabilities with a source URL; claim only the compliance language product-context.md uses
- Usage or scale numbers other than the ones under Verified proof points (older figures from decks, press or past pages are not valid; use the documented numbers exactly)
- Internal product-structure names as capitalized, branded product names unless {{WEBSITE}} and product-context.md position them that way
- Customer names or logos not listed under Verified proof points (former customers, logos from old pages, or names from sales conversations are not valid)

### Rule 7: Describe onboarding exactly as product-context.md documents it

When describing how to set up {{PRODUCT}}, follow the setup flow documented in product-context.md (Verified capabilities, and Migration help actually offered where setup help is involved) step by step. Do not fall back on a generic "log in and configure the admin dashboard" description, and do not invent setup steps, wizards or service tiers that aren't documented.

If product-context.md does not document a setup flow, halt and ask the orchestrator for one before writing any setup section.

Worked example (fictional): Acme's product-context.md documents this flow:

1. Connect your product's event stream
2. Choose an onboarding template from Acme's template library
3. Start building, OR request Acme's documented setup service

❌ Wrong: "Pick from the menu of features in your Acme admin dashboard"
❌ Wrong: "Toggle on the modules you want to use"
✓ Right: "Connect your event stream first, choose an onboarding template, then start building or request the setup service"

### Rule 8: NO contract buyout language

Never promise contract buyouts. Describe migration help only as documented under Migration help actually offered in product-context.md, using its wording and conditions. Never write phrases like:
- "We'll buy out your [Competitor] contract"
- "Switch and we'll cover your remaining contract"
- "Contract buyout available"

✓ Right (fictional Acme example, where product-context.md documents managed migration on request): "Migration support is available on request as part of Acme's setup service."

If Migration help actually offered is empty, say nothing about migration help beyond what {{DOCS_URL}} documents for self-serve import.

### Rule 9: Honest concession lines (3+ minimum on pillar)

PostHog pillars contain explicit "Choose [competitor] if..." statements. The {{PRODUCT}} version requires at least 3 of these on the pillar:

- One in the intro frame (e.g. "If you are a two-person team that only needs a single welcome checklist, do not migrate. Stay on Globex.") Draw these from the "Not a fit" line of the Ideal customer profile in product-context.md
- One in the "When to choose" decision section
- One in the FAQ ("Does Acme replace Globex? For product teams with multiple onboarding flows, yes. For a single static checklist, no.")
- "Good to know" callouts can carry additional concessions

This is the AEO/E-E-A-T move that wins citations from AI engines.

### Rule 10: Title patterns by post type

Verified from PostHog:

| Post type | Title pattern |
|---|---|
| Pillar | `In-depth: {{PRODUCT}} vs [Competitor]` |
| Alternatives listicle | `The most popular [Competitor] alternatives & competitors, compared` |
| Migration guide | `Migrate from [Competitor] to {{PRODUCT}}` |
| Narrative | `Why I switched from [Competitor] to {{PRODUCT}}` |
| Pricing explainer | `[Competitor] pricing explained` |
| 3-way comparison | `[Competitor] vs [Other] vs {{PRODUCT}}: which is right for your business` |
| B2B review | `Is [Competitor] good for B2B enterprises? An honest review` |
| Category explainer | `[Category A] vs [Category B] for B2B` |

Use the product name exactly as the first-mention rule in product-context.md specifies. Pillar slugs follow `{{PRODUCT_SLUG}}-vs-{competitor}`.

NO YEAR in any of these.

### Rule 11: Per-section structure for alternatives listicle

Each main entry must have these 6 sub-sections (verified from PostHog Mixpanel listicle):

1. `### What is [Tool]?`: 1-2 sentence definition
2. `### Key features`: bullet list of 5-7 features
3. `### Who uses [Tool]?`: typical user list + named customers (for the {{PRODUCT}} entry, only customers listed under Verified proof points in product-context.md)
4. `### How does [Tool] compare to [Competitor]?`: comparison table + Main differences (5 bullets) + Main similarities (5 bullets) + 1-2 paragraph discussion
5. `### Why do companies use [Tool]?`: numbered list of 3 reasons
6. `> #### Bottom line`: blockquote verdict (1-2 sentences)

Plus an "Honorable mentions" section with 5 bullet entries at end.
Plus "Which alternative should you choose?" decision section (4 bullet "if-then" lines).
Plus "Is {{PRODUCT}} right for you?" sales pitch section.
Plus FAQ with 12-14 questions.

### Rule 12: Internal link topology (bidirectional)

Every spoke must link to the pillar. The pillar must link to every spoke. All competitor mentions in body copy link to {{PRODUCT}}'s own internal pages, not to the competitor's website.

Required internal links from each post:
- Pillar → all 7 other spokes (in Related Reading + body)
- Listicle → pillar (multiple times in body) + other spokes (in FAQ + Related Reading)
- Migration guide → pillar + listicle + the services or migration-help page from product-context.md (if one exists)
- Narrative → pillar + listicle
- Pricing → pillar + listicle + 3-way
- 3-way → pillar + listicle + pricing
- Review → pillar + listicle + category
- Category → pillar + listicle + review

### Rule 13: Boilerplate footer

Every post ends with this italic blockquote, built from product-context.md (Core positioning statement, Verified capabilities, Verified proof points). Update it whenever product-context.md changes, and reuse it verbatim across every post in the cluster:

```
> {{PRODUCT}} is [category, from the Core positioning statement]. We provide [the main verified capabilities, as a short list]({{WEBSITE}}), [one clause on how they work together]. Trusted by [customer logos from Verified proof points] and [usage or scale number from Verified proof points, if one exists].
```

Worked example (fictional):

```
> Acme is a customer onboarding platform for B2B software teams. We provide [guided checklists, in-app messages, and activation analytics](https://acme.example/), connected so every new account gets a tailored first week. Trusted by Initech, Hooli, and more than 400 fictional software teams.
```

If Verified proof points lists no customer logos or numbers, drop the "Trusted by" sentence rather than inventing one.

Followed by `### Related reading` with the cluster's other posts as bullet links.

### Rule 14: REQUIRED quick-summary block on three post types

**Scope:** Three post types require a quick-summary block at the top, each with its own format:

1. **Alternatives listicle** (e.g. "Best Globex alternatives"): list of 4-5 tool entries
2. **3-way comparison** (e.g. "Globex vs Initech vs Acme"): list of 3 tool entries
3. **Pricing explainer** (e.g. "Globex pricing explained"): list of pricing tiers

**Not required for:** pillars (Rule 1 forbids it), narratives, migration guides, B2B reviews, or category explainers (when written as conceptual essays). These post types each have their own AEO-extractable opening pattern that doesn't need a list-format summary block.

#### Why these three post types specifically

The quick-summary block is an AEO move, not just a UX move. AI engines (ChatGPT, Perplexity, Bing Copilot, Google AI Overviews) extract from the top of a page first, and they cite list-formatted blocks more readily than prose paragraphs. The block earns the citation when the user's query is also list-shaped.

Mapping post types to their dominant AI-engine query:

| Post type | Dominant AI query | List-shaped? | Summary format |
|---|---|---|---|
| Pillar | "What's the difference between X and Y?" | No | None (numbered definition pair instead) |
| Alternatives listicle | "What are the best X alternatives?" | Yes (top-N list) | 4-5 tool entries |
| 3-way comparison | "Should I use X, Y, or Z?" | Yes (small set) | 3 tool entries |
| Pricing explainer | "How much does X cost?" | Yes (tier list) | 3-5 pricing tiers |
| Migration guide | "How do I migrate from X to Y?" | No (procedure) | None (banner blockquotes instead) |
| Narrative | "Why do people switch from X?" | No (story) | None |
| B2B review | "Is X good for B2B?" | No (verdict) | None (verdict-led intro) |
| Category explainer | "What's the difference between A and B?" | No (concept) | None (category definition intro) |

The three post types that get summary blocks are the three whose dominant query is list-shaped. The other five each have their own AEO-extractable opening that fits their query intent.

---

#### Format A: Alternatives listicle summary block

**When to use:** Posts of type "alternatives listicle", title pattern `The most popular [Competitor] alternatives & competitors, compared`.

**The 7 sub-rules:**

**14A.a. Position:** First paragraph of the post, immediately under the H1, before the intro paragraph that introduces the competitor and the guide.

**14A.b. Heading:** Bolded heading line containing the primary keyword. Format:

```
**The best [competitor] alternatives for [audience]:**
```

The primary keyword from the cluster plan must appear in the heading verbatim.

**14A.c. Length cap:** Total block under 120 words.

**14A.d. Per-entry cap:** Each entry max 25 words.

**14A.e. Format:** Each entry uses `- **Tool Name:** description`. Colon as separator (no em dash, per Rule 4).

**14A.f. {{PRODUCT}} locked into position 1:** {{PRODUCT}} is always the first entry, regardless of which competitors come after. Remaining entries follow the order they appear in the body.

**14A.g. Self-contained block:** No links, tables, or footnotes inside the block. Plain text only.

**14A.h. Closing editorial sentence:** One sentence (under 15 words) signaling deeper content below. Patterns:
- `Below, we compare each in depth.`
- `Read on for the full breakdown of each.`
- `See the full feature, pricing, and verdict comparison below.`

**Worked example (fictional Globex listicle, written for Acme):**

```markdown
# The most popular Globex alternatives & competitors, compared

**The best Globex alternatives for product and growth teams:**

- **Acme:** Customer onboarding platform for B2B software teams combining guided checklists, in-app messages, and activation analytics in one place.
- **Initech:** Product tour builder with a large template library and a visual editor, built for small marketing teams.
- **Umbrella:** Enterprise digital adoption platform with deep analytics and governance controls for large internal rollouts.
- **Hooli:** Lightweight in-app messaging tool with a generous free plan, closest direct match to Globex.

Below, we compare each in depth on features, pricing, and ICP fit.

[Standard contextual intro paragraph follows here.]
```

Audit: 92 words total, all entries under 25 words, Acme at position 1, primary keyword "Globex alternatives" in heading, closing sentence 12 words, self-contained.

---

#### Format B: 3-way comparison summary block

**When to use:** Posts of type "three_way", title pattern `[Competitor] vs [Other] vs {{PRODUCT}}: which is right for your business`.

**The 7 sub-rules:**

**14B.a. Position:** First paragraph after H1, before the contextual intro.

**14B.b. Heading:** Bolded heading line. Format:

```
**[Tool A] vs [Tool B] vs {{PRODUCT}} at a glance:**
```

The phrase "at a glance" signals the block is a fast-scan summary rather than a comparison list.

**14B.c. Length cap:** Total block under 100 words (tighter than listicle because there are only 3 entries plus a closing sentence).

**14B.d. Per-entry cap:** Each entry max 25 words.

**14B.e. Format:** `- **Tool Name:** description`. Colon as separator.

**14B.f. Order:** All three tools must appear, but {{PRODUCT}} does NOT have to be position 1 here (unlike the listicle). Order should match the comparison logic of the post (typically, the two competitors first, {{PRODUCT}} last as the "answer"; this is the more honest framing for a 3-way). However, if the post is positioned as recommending {{PRODUCT}}, {{PRODUCT}} can lead. Writer judgment.

**14B.g. Self-contained block:** No links, tables, or footnotes. Plain text only.

**14B.h. Closing editorial sentence:** One sentence (under 15 words) signaling depth below. Patterns:
- `Below, we compare each on features, pricing, and ICP fit.`
- `Read on for which one is right for your business.`
- `See the full breakdown to decide.`

**Worked example (fictional Globex vs Initech vs Acme):**

```markdown
# Globex vs Initech vs Acme: which is right for your business

**Globex vs Initech vs Acme at a glance:**

- **Globex:** Self-serve onboarding tool built for small teams shipping one welcome checklist and a handful of in-app tips.
- **Initech:** Product tour builder with a large template library and visual editor for marketing-led teams running feature launches.
- **Acme:** Customer onboarding platform for B2B software teams combining guided checklists, in-app messages, and activation analytics in one place.

Below, we compare each on features, pricing, and ICP fit.

[Standard contextual intro paragraph follows here.]
```

Audit: 76 words total, all entries under 25 words, all three tools defined, closing sentence 10 words, self-contained.

---

#### Format C: Pricing explainer summary block

**When to use:** Posts of type "pricing", title pattern `[Competitor] pricing explained`.

This format differs structurally because the entries are pricing tiers, not tools. The closing line is a competitive-position sentence (not a "below we compare" signal), because pricing-page readers want a quick competitive read at the top.

**The 7 sub-rules:**

**14C.a. Position:** First paragraph after H1, before the contextual intro.

**14C.b. Heading:** Bolded heading line containing the primary keyword. Format:

```
**[Competitor] pricing at a glance:**
```

The primary keyword (e.g. "Globex pricing") must appear in the heading verbatim.

**14C.c. Length cap:** Total block under 100 words.

**14C.d. Per-entry cap:** Each entry max 20 words (tighter than other formats because pricing entries are typically shorter).

**14C.e. Format:** `- **Tier name:** price + commission/condition + key inclusion`. Colon as separator.

**14C.f. Tier coverage:** All advertised tiers of the competitor must be listed, in order from cheapest to most expensive (or free → paid → enterprise). This is for accuracy and AEO completeness.

**14C.g. Self-contained block:** No links, tables, or footnotes inside the block. Pricing numbers in plain text. Currency symbols allowed.

**14C.h. Closing competitive sentence:** One sentence (under 25 words) that anchors {{PRODUCT}}'s competitive position. This is NOT "below we compare"; it's a {{PRODUCT}} positioning statement. The pricing model it states must match the Pricing section of product-context.md exactly. Pattern:

```
For comparison, {{PRODUCT}} uses [{{PRODUCT}}'s pricing model, from product-context.md], not [competitor's model].
```

This closing sentence is what wins the AI-engine citation because when ChatGPT pulls "[Competitor] pricing", it pulls the competitive line with it, and {{PRODUCT}} gets included in the answer.

**Worked example (fictional Globex pricing, written for Acme):**

```markdown
# Globex pricing explained

**Globex pricing at a glance:**

- **Free plan:** $0/month, up to 100 monthly active users
- **Starter:** ~$49/month, up to 1,000 monthly active users
- **Growth:** ~$199/month, up to 10,000 monthly active users
- **Enterprise:** custom quote, plus a one-time onboarding fee

For comparison, Acme uses a flat platform subscription with no per-user charges, regardless of how many accounts you onboard.

[Standard contextual intro paragraph follows here.]
```

Audit: 61 words total, all entries under 20 words, all advertised tiers covered, closing competitive sentence 19 words, self-contained, currency symbols allowed.

---

#### Why this rule overrides Rule 1 for these three post types

Rule 1 forbids quick-answer blocks on **pillars** because pillar readers want depth from the first paragraph. The numbered definition pair (`1. Competitor is X. 2. {{PRODUCT}} is Y.`) does the disambiguation work upfront on a pillar.

Rule 14 requires quick-summary blocks on **listicles, 3-way comparisons, and pricing explainers** because each of these post types has a list-shaped query intent that AI engines reward with citations. The summary block does the same disambiguation work but in scannable list format that matches each post type's reading pattern and dominant AI query.

All four rules are active simultaneously:
- Pillars: no summary block (Rule 1)
- Listicles, 3-way comparisons, pricing explainers: required summary block (Rule 14, with format A/B/C respectively)
- Narratives, migration guides, B2B reviews, category explainers: no summary block required (their AEO-extractable opening is built into their default opening pattern)

The quality-checker (skill 4) checks the right format for each post type and flags violations.



## Quality checklist before handoff

Before declaring a post complete, run:

```bash
cd posts/

echo "=== Universal checks (all post types) ==="

echo "Em dashes (should be 0):"
grep -c "—" *.md

echo "Year in H1 (should be 0):"
grep "^# " *.md | grep -c "20[0-9]\{2\}"

echo "External competitor links (should be 0):"
# Build the domain list from the Competitor set table in product-context.md
COMPETITOR_DOMAINS="competitor-one|competitor-two|competitor-three"
grep -E "https?://(www\.)?(${COMPETITOR_DOMAINS})\.[a-z]+" *.md

echo "Does NOT have claims (should be 0):"
# Build the term list from the Does NOT have section of product-context.md
DOES_NOT_HAVE_TERMS="term one\|term two\|term three"
grep -ci "${DOES_NOT_HAVE_TERMS}" *.md

echo "Buyout (should be 0):"
grep -ci "buyout" *.md

echo "Boilerplate footer present:"
# Use the opening sentence of the Rule 13 boilerplate built from product-context.md
grep -c "{{PRODUCT}} is [category, from the Core positioning statement]" *.md

echo ""
echo "=== Pillar-only check (Rule 1) ==="
echo "Quick answer block on pillar (should be 0, pillars don't have these):"
grep -c "Quick answer\|\*\*Quick answer\*\*" [pillar-slug].md

echo ""
echo "=== Listicle-only checks (Rule 14) ==="
echo "Quick-summary block heading present (should be 1):"
grep -c "^\*\*The best .* alternatives for" [listicle-slug].md

echo "{{PRODUCT}} is the first bolded entry in summary block (should be 1):"
# Pull the first bolded entry inside the summary block. It must be {{PRODUCT}}
python3 -c "
import re
with open('[listicle-slug].md') as f:
    content = f.read()
# Find the summary block (between heading line and the next blank line + paragraph)
m = re.search(r'\*\*The best .* alternatives for[^*]*\*\*\n\n((?:- \*\*[^*]+\*\*:[^\n]*\n)+)', content)
if m:
    first_entry = m.group(1).split('\n')[0]
    if first_entry.startswith('- **{{PRODUCT}}:**'):
        print('1')
    else:
        print('0: first entry is:', first_entry)
else:
    print('0: summary block not detected')
"

echo "Summary block under 120 words:"
python3 -c "
import re
with open('[listicle-slug].md') as f:
    content = f.read()
m = re.search(r'\*\*The best[^*]*\*\*\n\n((?:- \*\*[^\n]+\n)+\n[^\n]+\.)', content)
if m:
    block = m.group(0)
    wc = len(block.split())
    print(f'{wc} words: {\"PASS\" if wc <= 120 else \"FAIL\"}')
else:
    print('summary block not detected')
"

echo "Each entry in summary block under 25 words:"
python3 -c "
import re
with open('[listicle-slug].md') as f:
    content = f.read()
entries = re.findall(r'^- \*\*[^*]+\*\*:?\s*([^\n]+)', content, re.MULTILINE)
fail = False
for i, entry in enumerate(entries[:5], 1):  # check first 5 (the summary block)
    wc = len(entry.split())
    if wc > 25:
        print(f'Entry {i} has {wc} words: FAIL')
        fail = True
if not fail:
    print('all under 25 words: PASS')
"

echo "Closing editorial sentence after summary block (no bold tool name):"
# Manual check: a sentence ends the summary block, comes between the last bullet and the next paragraph
```

All checks must pass before handoff to QA. If any check fails, fix before declaring complete.

## Failure modes from earlier iterations

These are the mistakes that cost the most rework. The rules above exist because of these:

1. **Inventing features.** Cost: 5+ corrections in an earlier cluster run. Mitigation: Rule 6.
2. **Putting year in headlines.** Cost: caught at audit. Mitigation: Rule 2.
3. **Adding quick-answer blocks.** Cost: caught at audit. Mitigation: Rule 1.
4. **Linking to competitor sites.** Cost: caught at audit. Mitigation: Rule 3.
5. **Using internal product-structure branding that {{WEBSITE}} doesn't use.** Cost: positioning drift. Mitigation: Rule 6 + read homepage.
6. **Describing {{PRODUCT}} onboarding as generic UI option-picking instead of the documented flow.** Cost: misrepresents the actual flow. Mitigation: Rule 7.
7. **Buyout language, or migration help not listed in product-context.md.** Cost: contradicts company policy. Mitigation: Rule 8.
8. **Em dashes leaking through.** Cost: voice rule violation. Mitigation: Rule 4 + sed pass.

When in doubt, re-fetch the PostHog source of truth and the {{PRODUCT}} homepage ({{WEBSITE}}), and re-read product-context.md. Both are linked in the "Required reference reads" section above.
