---
name: cluster-quality-checker
description: Audits cluster posts against the 13 strict PostHog template rules and outputs a pass/fail report with specific corrections. Use this skill after the writer hands off a draft and before the post goes to publish. Trigger on phrases like "QA this post", "quality check the pillar", "audit the listicle", "did the writer follow the rules", "run the checklist on these posts", or any handoff from the writer or orchestrator that asks for verification before publish. This skill does not rewrite content. It produces a structured report that the writer or human editor uses to fix issues. Trigger aggressively on any cluster post entering the QA stage.
---

# Cluster Quality Checker

This skill is the auditor that sits between the writer and the publish step. It reads draft posts and grades them against the strict rules codified in the writer skill (`cluster-writer/SKILL.md`). Every claim about the product is checked against `product-context.md`, the filled product context file at the root of the working folder (next to `clusters/`). Output is a structured report listing every violation with the exact line number and a recommended fix. The skill never rewrites content — that's the writer's job on the next pass.

## When to use this skill

Trigger when:
- The writer skill has just finished a post (the orchestrator should call QA automatically)
- A user says "QA this", "audit this", "check this against the rules", "run the checklist"
- A post is being prepared for publish and needs a final gate
- A revision cycle is happening (re-QA after the writer fixes issues)

Required inputs:
- One or more `.md` post files at known paths
- The writer skill's rule reference (`cluster-writer/SKILL.md`)
- The filled product context file (`product-context.md` at the working-folder root, next to `clusters/`)

If the writer skill or `product-context.md` aren't accessible, halt and ask the orchestrator to provide them. Do not invent rules from memory.

## What the QA report looks like

Output is a single markdown file at `qa-reports/[slug]-qa-report.md` with this structure:

```markdown
# QA Report: [post title]

**Post:** [slug]
**Audited:** [date]
**Verdict:** PASS | PASS WITH MINOR FIXES | FAIL — REVISIONS REQUIRED

## Summary

- Hard rules passed: X / 13
- Soft rules passed: X / 12
- Total violations: N
- Severity breakdown: [count by severity]

## Hard rule violations (block publish)

[Listed by severity, with line numbers and recommended fixes]

## Soft rule violations (recommended fixes)

[Listed by severity, with line numbers and recommended fixes]

## What passed

[Quick affirmation of the rules that did pass, so the writer sees what's working]

## Recommended next action

[Either "Ship as-is", "Writer fixes hard violations then re-QA", or "Revise comprehensively"]
```

## The 14 hard rules (block publish if violated)

These come directly from `cluster-writer/SKILL.md`. Quote the rule number and run a deterministic check.

### Rule 1: No quick-answer block at top of pillars
**Scope:** Pillar posts only. (Listicles must have a quick-summary block — see Rule 14.)
**Check:** On pillar posts, grep for `Quick answer:` or `**Quick answer**` in the first 30 lines after the H1.
**Fail condition:** any match on a pillar.
**Severity:** HARD.
**Fix:** Remove the block. Replace with direct intro paragraph + numbered definition pair (verify the post follows PostHog's exact pattern from posthog-template-audit.md).

### Rule 2: No year in any headline
**Check:** Match `^#{1,6} ` (any heading level) and check for `\b20[0-9]{2}\b` in the heading text.
**Fail condition:** any match.
**Severity:** HARD.
**Fix:** Remove year. PostHog never date-stamps headlines because it forces yearly content updates.

### Rule 3: No external links to competitor websites
**Check:** Match URLs in markdown links `\[.*?\]\(https?://.*?\)`. Filter out `{{WEBSITE}}`, `{{DOCS_URL}}`, `posthog.com`. Flag any remaining external URL where the domain matches a known competitor domain. Build the competitor domain list from the Competitor set section of `product-context.md` (every competitor in that table, plus any competitor named in the cluster plan).
**Fail condition:** any match.
**Severity:** HARD.
**Fix:** Either unlink the competitor mention or replace with internal link to {{PRODUCT}}'s own page covering that competitor.

### Rule 4: No em dashes
**Check:** grep for `—` (the em dash character, U+2014) in body text.
**Fail condition:** any match.
**Severity:** HARD.
**Fix:** Run the writer's em-dash replacement pass (period before capital letter, comma before lowercase).

### Rule 5: No ampersands in body copy
**Check:** Match `&` in body text. Allow ampersands in (a) the H1/H2 titles when matching PostHog's "& competitors, compared" pattern, (b) inside HTML entities like `&amp;`, (c) inside code blocks.
**Fail condition:** ampersand outside the allowed contexts.
**Severity:** HARD.
**Fix:** Replace with "and".

### Rule 6: No invented {{PRODUCT}} features
**Check:** Cross-reference every feature claim about {{PRODUCT}} against the verified inventory in `product-context.md` (Verified capabilities, Integrations, Pricing, Verified proof points). Specifically scan for these high-risk strings:
- "CRM integration credentials" or similar
- "SOC 2" or "SOC 2 certified" (only allowed if `product-context.md` confirms certification)
- "Native Salesforce integration" or "Native HubSpot integration" as standalone CRM apps (only allowed if listed under Integrations)
- Any usage or scale number (users, countries, customers) that does not match the numbers in Verified proof points exactly
- Any product-area name written as a capitalized product name when `product-context.md` does not write it that way
- Any customer name or logo not listed under Customer logos in Verified proof points
- Any customer story or case study reference when Verified proof points says none exist
- Any item listed in the Does NOT have section of `product-context.md`
- Any specific compliance certification name not in the inventory

**Fail condition:** any unverified claim.
**Severity:** HARD.
**Fix:** Replace with the verified equivalent from `product-context.md`, or remove the claim entirely.

### Rule 7: {{PRODUCT}} onboarding flow described correctly
**Check:** When the post describes how to set up {{PRODUCT}} (typically in pillar's "How is {{PRODUCT}} different" section, listicle's "Why companies use {{PRODUCT}}" section, or migration guide's Step 2), verify the flow matches the setup or onboarding steps described in `product-context.md` (Verified capabilities and Migration help actually offered), step for step and in the same order.

**Fail condition:** any setup step not described in `product-context.md`, or generic invented descriptions like "pick from the menu of features", "toggle on modules", "select your modules from the dashboard", "configure your module settings".
**Severity:** HARD.
**Fix:** Rewrite to the verified onboarding flow from `product-context.md`.

### Rule 8: No buyout language
**Check:** grep for `buyout`, `contract buyout`, `we'll cover your`, `we'll buy out`, `cover your remaining contract`.
**Fail condition:** any match (case-insensitive).
**Severity:** HARD.
**Fix:** Replace with a sentence describing only the migration help listed in the Migration help actually offered section of `product-context.md`. If that section is empty, remove the migration-help claim.

### Rule 9: Honest concession lines (3+ on pillar)
**Check:** On pillar posts only, count occurrences of phrases like:
- "Choose [competitor] if"
- "Stay on [competitor]"
- "do not migrate"
- "use [competitor] instead"
- "[competitor] wins"
- "[competitor] is better"
- "**For [not-a-fit segment], no.**" (segments from Ideal customer profile, Not a fit, in `product-context.md`)

**Fail condition:** fewer than 3 concessions on a pillar post.
**Severity:** HARD on pillar; SOFT on other post types.
**Fix:** Add concessions in: intro frame, "When to choose" section, FAQ, and Good-to-know callouts.

### Rule 10: Title pattern matches the post type
**Check:** Match the H1 against the title pattern table:
- Pillar: `In-depth: {{PRODUCT}} vs [Competitor]`
- Listicle: `The most popular [Competitor] alternatives & competitors, compared`
- Migration: `Migrate from [Competitor] to {{PRODUCT}}`
- Narrative: `Why I switched from [Competitor] to {{PRODUCT}}`
- Pricing: `[Competitor] pricing explained`
- 3-way: `[Competitor] vs [Other] vs {{PRODUCT}}: which is right for your business`
- Review: `Is [Competitor] good for B2B enterprises? An honest review`
- Category: `[Category A] vs [Category B] for B2B`

**Fail condition:** H1 doesn't match the expected pattern for the declared post type.
**Severity:** HARD.
**Fix:** Update H1 to match the verified pattern.

### Rule 11: Listicle entry structure (6 sub-sections per main entry)
**Check (listicles only):** Each main entry (## numbered heading) must have these 6 sub-sections in this order:
1. `### What is [Tool]?`
2. `### Key features`
3. `### Who uses [Tool]?`
4. `### How does [Tool] compare to [Competitor]?`
5. `### Why do companies use [Tool]?`
6. `> #### Bottom line` (blockquote verdict)

Plus: "Honorable mentions", "Which alternative should you choose?", "Is {{PRODUCT}} right for you?", FAQ.

**Fail condition:** any main entry missing one of the 6 sub-sections, or the cluster-level sections missing.
**Severity:** HARD.
**Fix:** Add the missing sub-section.

### Rule 12: Internal link topology bidirectional
**Check:** Every spoke must link to the pillar at least once. The pillar must link to every spoke (matched by slug in the link href).
**Fail condition:** any spoke without a pillar link, or pillar missing a link to any spoke that exists in the cluster.
**Severity:** HARD on pillar/listicle; SOFT on other spokes if they cross-link to 2+ siblings.
**Fix:** Add the missing internal link, ideally with a keyword-rich anchor.

### Rule 13: Boilerplate footer present
**Check:** Match the boilerplate footer string near the end of the post. The boilerplate is built from `product-context.md` and follows this shape:
> {{PRODUCT}} is [category line from Positioning]. We provide [the product's core capabilities, linked](...), [one clause from the core positioning statement]. Trusted by [customer logos from Verified proof points] and [scale number from Verified proof points, if any].

The trust line may only use customer names and numbers that appear in Verified proof points. If none are listed, drop the trust line rather than inventing one.

**Fail condition:** missing or substantially modified boilerplate.
**Severity:** HARD.
**Fix:** Re-add the boilerplate.

### Rule 14: REQUIRED quick-summary block on three post types
**Scope:** Three post types require a quick-summary block, each with its own format:
1. **Alternatives listicle** (post_type `listicle`) — Format A: 4-5 tool entries
2. **3-way comparison** (post_type `three_way`) — Format B: 3 tool entries
3. **Pricing explainer** (post_type `pricing`) — Format C: pricing tier list

Skipped for all other post types (pillar, narrative, migration, review, category).

#### Format A: Alternatives listicle (post_type = "listicle")

**Check 14A.a — Heading present:** First paragraph after H1 starts with a bolded heading of the form `**The best [keyword] alternatives for [audience]:**` containing the cluster's primary keyword.

**Check 14A.b — Total length under 120 words:** Block from heading line through closing editorial sentence under 120 words.

**Check 14A.c — Per-entry length under 25 words:** Each entry's description (text after the colon) under 25 words.

**Check 14A.d — {{PRODUCT}} is the first entry:** First bulleted entry starts with `- **{{PRODUCT}}:**`.

**Check 14A.e — All entries use bold-tool-name pattern:** Every entry matches `- **Tool Name:** description`.

**Check 14A.f — Self-contained block:** No markdown links, tables, or footnotes inside the block.

**Check 14A.g — Closing editorial sentence:** A non-bulleted sentence after the last entry signals "more content below" (under 15 words). Patterns: "Below, we compare...", "Read on for...", "See the full breakdown...".

#### Format B: 3-way comparison (post_type = "three_way")

**Check 14B.a — Heading present:** First paragraph after H1 starts with a bolded heading of the form `**[Tool A] vs [Tool B] vs {{PRODUCT}} at a glance:**`. The phrase "at a glance" must appear.

**Check 14B.b — Total length under 100 words.**

**Check 14B.c — Per-entry length under 25 words.**

**Check 14B.d — Exactly 3 entries:** No more, no fewer.

**Check 14B.e — All entries use bold-tool-name pattern:** Every entry matches `- **Tool Name:** description`.

**Check 14B.f — All three tools defined:** All three tool names from the H1 must appear as bolded entries.

**Check 14B.g — Self-contained block:** No links, tables, or footnotes.

**Check 14B.h — Closing editorial sentence:** Non-bulleted sentence after the last entry signals depth below (under 15 words).

#### Format C: Pricing explainer (post_type = "pricing")

**Check 14C.a — Heading present:** First paragraph after H1 starts with a bolded heading of the form `**[Competitor] pricing at a glance:**` containing the primary keyword (e.g. "Globex pricing").

**Check 14C.b — Total length under 100 words.**

**Check 14C.c — Per-entry length under 20 words.**

**Check 14C.d — All entries use bold-tier-name pattern:** Every entry matches `- **Tier name:** description`.

**Check 14C.e — Self-contained block:** No links or tables. Currency symbols ($, €, £) allowed.

**Check 14C.f — Closing competitive sentence:** A non-bulleted sentence after the last entry must mention {{PRODUCT}} and a pricing-positioning claim taken from the Pricing section of `product-context.md`. Pattern: "For comparison, {{PRODUCT}} uses [model], not [competitor model]." Under 25 words.

**Fail condition (all formats):** any sub-check missing or violated.
**Severity:** HARD on listicle, three_way, pricing post types. N/A (skip) on other post types.
**Fix:** Re-add the summary block per the appropriate worked example in Rule 14 of `cluster-writer/SKILL.md`.

#### Audit script for Rule 14

```python
# Set these from product-context.md before running the audit.
PRODUCT = "{{PRODUCT}}"  # product name as written on first mention (Brand basics)

def check_rule_14(content, post_type):
    """Returns list of violation dicts. Routes to format A, B, or C based on post_type."""
    if post_type == "listicle":
        return _check_rule_14_format_A(content)
    elif post_type == "three_way":
        return _check_rule_14_format_B(content)
    elif post_type == "pricing":
        return _check_rule_14_format_C(content)
    else:
        return []  # Rule 14 doesn't apply to this post type



def _extract_block_text(content, heading_match):
    """Extract just the summary block: heading + blank + bullets + blank + ONE closing sentence.

    Walks lines deterministically rather than using a regex boundary that can overshoot.
    """
    heading_pos = content.index(heading_match.group(0))
    after_heading = content[heading_pos:]
    lines = after_heading.split('\n')

    block_lines = [lines[0]]  # heading
    i = 1

    # Skip blank line(s) after heading
    while i < len(lines) and not lines[i].strip():
        block_lines.append(lines[i])
        i += 1

    # Capture all bulleted entries
    while i < len(lines) and lines[i].strip().startswith('-'):
        block_lines.append(lines[i])
        i += 1

    # Skip blank line(s) between bullets and closing sentence
    while i < len(lines) and not lines[i].strip():
        block_lines.append(lines[i])
        i += 1

    # Capture exactly ONE closing sentence (single non-empty, non-bullet line)
    if i < len(lines) and lines[i].strip() and not lines[i].strip().startswith('-'):
        block_lines.append(lines[i])

    return '\n'.join(block_lines)


def _check_rule_14_format_A(content):
    """Alternatives listicle — 4-5 entries, {{PRODUCT}} at position 1."""
    violations = []

    heading_match = re.search(
        r'^\*\*The best (.+?) alternatives for (.+?):\*\*\s*$',
        content,
        re.MULTILINE
    )
    if not heading_match:
        violations.append({
            "rule": "14A.a",
            "found": "Listicle quick-summary heading not detected",
            "fix": "Add bolded heading: **The best [keyword] alternatives for [audience]:** as the first paragraph after H1."
        })
        return violations

    block_text = _extract_block_text(content, heading_match)

    word_count = len(block_text.split())
    if word_count > 120:
        violations.append({
            "rule": "14A.b",
            "found": f"Block is {word_count} words (cap is 120)",
            "fix": "Tighten entry descriptions. Do not extend beyond 4-5 entries."
        })

    entries = re.findall(r'^- \*\*([^:*]+):\*\*\s*(.+)$', block_text, re.MULTILINE)
    if entries and entries[0][0].strip() != PRODUCT:
        violations.append({
            "rule": "14A.d",
            "found": f"First entry is '{entries[0][0].strip()}', not '{PRODUCT}'",
            "fix": f"Move {PRODUCT} to position 1 in the summary block."
        })

    for tool_name, description in entries:
        wc = len(description.split())
        if wc > 25:
            violations.append({
                "rule": "14A.c",
                "found": f"Entry for '{tool_name.strip()}' is {wc} words (cap is 25)",
                "fix": f"Tighten the {tool_name.strip()} description to under 25 words."
            })

    if re.search(r'\[[^\]]+\]\([^\)]+\)', block_text):
        violations.append({
            "rule": "14A.f",
            "found": "Markdown link detected inside summary block",
            "fix": "Remove all links from the summary block. It must be self-contained plain text."
        })
    if '|' in block_text:
        violations.append({
            "rule": "14A.f",
            "found": "Pipe character detected (possible table) inside summary block",
            "fix": "Remove all tables from the summary block."
        })

    lines = [l.strip() for l in block_text.strip().split('\n') if l.strip()]
    last_line = lines[-1] if lines else ""
    if last_line.startswith('-') or last_line.startswith('**The best'):
        violations.append({
            "rule": "14A.g",
            "found": "No closing editorial sentence after the last entry",
            "fix": "Add a closing sentence under 15 words signaling depth below."
        })
    elif last_line and len(last_line.split()) > 15:
        violations.append({
            "rule": "14A.g",
            "found": f"Closing sentence is {len(last_line.split())} words (cap is 15)",
            "fix": "Tighten closing sentence to under 15 words."
        })

    return violations


def _check_rule_14_format_B(content):
    """3-way comparison — exactly 3 entries, all 3 tools defined."""
    violations = []

    heading_match = re.search(
        r'^\*\*(.+?) vs (.+?) vs (.+?) at a glance:\*\*\s*$',
        content,
        re.MULTILINE
    )
    if not heading_match:
        violations.append({
            "rule": "14B.a",
            "found": "3-way summary heading not detected",
            "fix": f"Add bolded heading: **[Tool A] vs [Tool B] vs {PRODUCT} at a glance:** as the first paragraph after H1."
        })
        return violations

    expected_tools = [heading_match.group(1).strip(),
                      heading_match.group(2).strip(),
                      heading_match.group(3).strip()]

    block_text = _extract_block_text(content, heading_match)

    word_count = len(block_text.split())
    if word_count > 100:
        violations.append({
            "rule": "14B.b",
            "found": f"Block is {word_count} words (cap is 100)",
            "fix": "Tighten entry descriptions."
        })

    entries = re.findall(r'^- \*\*([^:*]+):\*\*\s*(.+)$', block_text, re.MULTILINE)

    if len(entries) != 3:
        violations.append({
            "rule": "14B.d",
            "found": f"Found {len(entries)} entries, expected exactly 3",
            "fix": "3-way comparison summary block must have exactly 3 tool entries."
        })

    for tool_name, description in entries:
        wc = len(description.split())
        if wc > 25:
            violations.append({
                "rule": "14B.c",
                "found": f"Entry for '{tool_name.strip()}' is {wc} words (cap is 25)",
                "fix": f"Tighten description to under 25 words."
            })

    found_tools = [e[0].strip() for e in entries]
    for expected in expected_tools:
        if expected not in found_tools:
            violations.append({
                "rule": "14B.f",
                "found": f"Tool '{expected}' from H1 not found in summary block",
                "fix": f"Add an entry for '{expected}' in the summary block."
            })

    if re.search(r'\[[^\]]+\]\([^\)]+\)', block_text) or '|' in block_text:
        violations.append({
            "rule": "14B.g",
            "found": "Link or table detected in summary block",
            "fix": "Remove links and tables. Plain text only."
        })

    lines = [l.strip() for l in block_text.strip().split('\n') if l.strip()]
    last_line = lines[-1] if lines else ""
    if last_line.startswith('-') or last_line.startswith('**'):
        violations.append({
            "rule": "14B.h",
            "found": "No closing editorial sentence after last entry",
            "fix": "Add closing sentence under 15 words signaling depth below."
        })
    elif last_line and len(last_line.split()) > 15:
        violations.append({
            "rule": "14B.h",
            "found": f"Closing sentence is {len(last_line.split())} words (cap is 15)",
            "fix": "Tighten closing sentence to under 15 words."
        })

    return violations


def _check_rule_14_format_C(content):
    """Pricing explainer — pricing tiers, closing competitive sentence."""
    violations = []

    heading_match = re.search(
        r'^\*\*(.+?) pricing at a glance:\*\*\s*$',
        content,
        re.MULTILINE
    )
    if not heading_match:
        violations.append({
            "rule": "14C.a",
            "found": "Pricing summary heading not detected",
            "fix": "Add bolded heading: **[Competitor] pricing at a glance:** as the first paragraph after H1."
        })
        return violations

    block_text = _extract_block_text(content, heading_match)

    word_count = len(block_text.split())
    if word_count > 100:
        violations.append({
            "rule": "14C.b",
            "found": f"Block is {word_count} words (cap is 100)",
            "fix": "Tighten tier descriptions."
        })

    entries = re.findall(r'^- \*\*([^:*]+):\*\*\s*(.+)$', block_text, re.MULTILINE)

    for tier_name, description in entries:
        wc = len(description.split())
        if wc > 20:
            violations.append({
                "rule": "14C.c",
                "found": f"Tier '{tier_name.strip()}' is {wc} words (cap is 20)",
                "fix": f"Tighten {tier_name.strip()} description to under 20 words."
            })

    if re.search(r'\[[^\]]+\]\([^\)]+\)', block_text) or '|' in block_text:
        violations.append({
            "rule": "14C.e",
            "found": "Link or table detected in summary block",
            "fix": "Remove links and tables. Plain text only (currency symbols allowed)."
        })

    lines = [l.strip() for l in block_text.strip().split('\n') if l.strip()]
    last_line = lines[-1] if lines else ""
    if last_line.startswith('-') or last_line.startswith('**'):
        violations.append({
            "rule": "14C.f",
            "found": "No closing competitive sentence after last entry",
            "fix": f"Add closing sentence mentioning {PRODUCT}'s pricing model. Pattern: 'For comparison, {PRODUCT} uses [model], not [competitor model].'"
        })
    elif PRODUCT not in last_line:
        violations.append({
            "rule": "14C.f",
            "found": f"Closing sentence does not mention {PRODUCT}: '{last_line[:80]}...'",
            "fix": f"Closing sentence must reference {PRODUCT}'s pricing model for AEO citation."
        })
    elif last_line and len(last_line.split()) > 25:
        violations.append({
            "rule": "14C.f",
            "found": f"Closing sentence is {len(last_line.split())} words (cap is 25)",
            "fix": "Tighten closing competitive sentence to under 25 words."
        })

    return violations
```

This function gets called from the main `audit_post()` function whenever `post_type in ("listicle", "three_way", "pricing")`. The router function `check_rule_14()` dispatches to the right format-specific checker.

## The 12 soft rules (recommended but not blocking)

### Soft rule 1: Word count in expected range per post type
- Pillar: 3,000–4,500 words
- Listicle: 3,000–4,000 words
- Migration: 1,200–2,000 words
- Narrative: 800–1,200 words
- Pricing: 1,500–2,500 words
- 3-way: 2,000–3,000 words
- Review: 1,200–2,000 words
- Category: 1,500–2,500 words

### Soft rule 2: At least one comparison table per major H2 section in pillars and listicles

### Soft rule 3: At least one "Good to know" callout per major H2 section in pillars

### Soft rule 4: FAQ count meets minimum
- Pillar: 18+ questions
- Listicle: 12+ questions
- Migration: 5+ inline questions or none (HowTo schema is the primary signal)
- Other types: 8+ questions

### Soft rule 5: All "Choose X" decision lines use bold tool names

### Soft rule 6: All comparison-table rows use ✓ / ✗ / Partial (not "Yes/No/Limited")

### Soft rule 7: Schema declared in frontmatter
- Pillar: Article + FAQPage + BreadcrumbList
- Listicle: Article + ItemList + FAQPage
- Migration: TechArticle + HowTo
- Others: Article + (FAQPage if FAQs present)

### Soft rule 8: H1 matches title in frontmatter (no drift)

### Soft rule 9: Slug in frontmatter matches the filename

### Soft rule 10: Date in frontmatter is in the past or today (no future-dating)

### Soft rule 11: Author byline present in frontmatter

### Soft rule 12: Meta title and meta description present in frontmatter, under character limits (60 chars title, 160 chars description)

## Audit script

For each post, run this audit pass:

```python
import re
import os
from pathlib import Path

# Set these from product-context.md before running the audit.
PRODUCT = "{{PRODUCT}}"  # product name as written on first mention (Brand basics)
BOILERPLATE_SIGNAL = "[first 8+ words of the boilerplate built from product-context.md]"
COMPETITOR_DOMAINS = ["globex.example"]  # replace with every competitor domain from the Competitor set


def _section_bullets(md, heading):
    """Return the bullet lines under a '## heading' section of product-context.md."""
    m = re.search(rf"^## {re.escape(heading)}\s*$(.*?)(?=^## |\Z)", md, re.MULTILINE | re.DOTALL)
    if not m:
        return []
    return [b.strip("- ").strip() for b in m.group(1).split("\n")
            if b.strip().startswith("-") and b.strip("- ").strip()]


def audit_post(post_path, post_type, product_context_path):
    with open(post_path) as f:
        content = f.read()
    with open(product_context_path) as f:
        verified_inventory = f.read()

    violations = {"hard": [], "soft": []}
    lines = content.split("\n")

    # Rule 1: No quick-answer block (pillars only — listicles use a quick-summary block per Rule 14)
    if post_type == "pillar":
        for i, line in enumerate(lines[:30]):
            if "Quick answer:" in line or "**Quick answer**" in line:
                violations["hard"].append({
                    "rule": 1,
                    "line": i + 1,
                    "found": line.strip(),
                    "fix": "Remove the quick-answer block. Replace with direct intro paragraph + numbered definition pair."
                })

    # Rule 14: REQUIRED quick-summary block (listicles and categories only)
    if post_type in ("listicle", "three_way", "pricing"):
        violations["hard"].extend(check_rule_14(content, post_type))

    # Rule 2: No year in headlines
    for i, line in enumerate(lines):
        if re.match(r"^#{1,6} ", line):
            if re.search(r"\b20[0-9]{2}\b", line):
                violations["hard"].append({
                    "rule": 2,
                    "line": i + 1,
                    "found": line.strip(),
                    "fix": "Remove the year from the heading. PostHog never date-stamps headlines."
                })

    # Rule 3: External competitor links
    competitor_domains = COMPETITOR_DOMAINS
    for i, line in enumerate(lines):
        for match in re.finditer(r"\[([^\]]+)\]\((https?://[^\)]+)\)", line):
            url = match.group(2)
            for domain in competitor_domains:
                if domain in url:
                    violations["hard"].append({
                        "rule": 3,
                        "line": i + 1,
                        "found": match.group(0),
                        "fix": f"Either unlink '{match.group(1)}' or replace with internal {PRODUCT} link covering {domain}."
                    })

    # Rule 4: Em dashes
    for i, line in enumerate(lines):
        if "—" in line:
            violations["hard"].append({
                "rule": 4,
                "line": i + 1,
                "found": line.strip()[:120],
                "fix": "Replace em dash with period (followed by capital letter) or comma (followed by lowercase)."
            })

    # Rule 5: Ampersands in body
    in_code_block = False
    for i, line in enumerate(lines):
        if line.strip().startswith("```"):
            in_code_block = not in_code_block
            continue
        if in_code_block:
            continue
        if line.startswith("#"):  # headings allowed
            continue
        if "&amp;" in line:  # HTML entity allowed
            continue
        if "&" in line:
            violations["hard"].append({
                "rule": 5,
                "line": i + 1,
                "found": line.strip()[:120],
                "fix": "Replace '&' with 'and' in body copy."
            })

    # Rule 6: Invented features (high-risk strings)
    invented_strings = [
        "CRM integration credentials",
        "CRM credentials",
        "SOC 2 certified",
        "Native Salesforce integration",
        "Native HubSpot integration"
    ]
    # Everything the product does NOT have is always a violation, even if it appears in the file.
    does_not_have = _section_bullets(verified_inventory, "Does NOT have")
    for i, line in enumerate(lines):
        for missing in does_not_have:
            if missing.lower() in line.lower():
                violations["hard"].append({
                    "rule": 6,
                    "line": i + 1,
                    "found": missing,
                    "fix": "Listed under Does NOT have in product-context.md. Remove the claim."
                })
    for i, line in enumerate(lines):
        for risky in invented_strings:
            if risky.lower() in line.lower() and risky.lower() not in verified_inventory.lower():
                violations["hard"].append({
                    "rule": 6,
                    "line": i + 1,
                    "found": risky,
                    "fix": f"Replace with verified equivalent from product-context.md or remove."
                })

    # Rule 7: Onboarding flow
    bad_phrases = [
        "pick from the menu",
        "toggle on modules",
        "select your modules",
        "configure your module settings",
        "choose which features to enable"
    ]
    for i, line in enumerate(lines):
        for bad in bad_phrases:
            if bad.lower() in line.lower():
                violations["hard"].append({
                    "rule": 7,
                    "line": i + 1,
                    "found": line.strip()[:120],
                    "fix": "Rewrite to the setup flow described in product-context.md, step for step."
                })

    # Rule 8: Buyout language
    for i, line in enumerate(lines):
        if re.search(r"\b(buyout|buy out|cover your remaining contract)\b", line, re.IGNORECASE):
            violations["hard"].append({
                "rule": 8,
                "line": i + 1,
                "found": line.strip()[:120],
                "fix": "Replace with the migration help listed under Migration help actually offered in product-context.md, or remove."
            })

    # Rule 9: Concessions on pillar
    if post_type == "pillar":
        concession_patterns = [
            r"Choose \w+ if",
            r"Stay on \w+",
            r"do not migrate",
            r"use \w+ instead",
            r"\w+ wins",
            r"\*\*For [^*]+, no\.\*\*",
            r"is better for"
        ]
        concession_count = sum(
            1 for pattern in concession_patterns
            for line in lines
            if re.search(pattern, line, re.IGNORECASE)
        )
        if concession_count < 3:
            violations["hard"].append({
                "rule": 9,
                "line": 0,
                "found": f"Only {concession_count} concession lines found (minimum 3 on pillar).",
                "fix": "Add 'Choose [competitor] if...' lines in intro frame, decision section, and FAQ."
            })

    # Rule 10: Title pattern
    h1 = next((l for l in lines if l.startswith("# ")), None)
    expected_patterns = {
        "pillar": rf"^# In-depth: {re.escape(PRODUCT)} vs",
        "listicle": r"^# The most popular .+ alternatives.* competitors, compared",
        "migration": rf"^# Migrate from .+ to {re.escape(PRODUCT)}",
        "narrative": r"^# Why I switched from",
        "pricing": r"^# .+ pricing explained",
        "three_way": rf"^# .+ vs .+ vs {re.escape(PRODUCT)}",
        "review": r"^# Is .+ good for B2B",
        "category": r"^# .+ vs .+ for B2B"
    }
    if h1 and post_type in expected_patterns:
        if not re.search(expected_patterns[post_type], h1):
            violations["hard"].append({
                "rule": 10,
                "line": lines.index(h1) + 1,
                "found": h1.strip(),
                "fix": f"Update H1 to match the {post_type} title pattern."
            })

    # Rule 13: Boilerplate footer
    boilerplate_signal = BOILERPLATE_SIGNAL
    if boilerplate_signal not in content:
        violations["hard"].append({
            "rule": 13,
            "line": 0,
            "found": "Boilerplate footer missing.",
            "fix": "Add the standard boilerplate built from product-context.md before Related reading."
        })

    # SOFT rules: word count
    word_count = len(content.split())
    expected_ranges = {
        "pillar": (3000, 4500),
        "listicle": (3000, 4000),
        "migration": (1200, 2000),
        "narrative": (800, 1200),
        "pricing": (1500, 2500),
        "three_way": (2000, 3000),
        "review": (1200, 2000),
        "category": (1500, 2500)
    }
    if post_type in expected_ranges:
        low, high = expected_ranges[post_type]
        if word_count < low:
            violations["soft"].append({
                "rule": "S1",
                "found": f"Word count {word_count} is below expected range {low}-{high}",
                "fix": f"Expand to at least {low} words."
            })
        elif word_count > high:
            violations["soft"].append({
                "rule": "S1",
                "found": f"Word count {word_count} exceeds expected range {low}-{high}",
                "fix": f"Trim to under {high} words."
            })

    return violations
```

## How to call this skill

When the orchestrator hands off a draft for QA:

1. Identify the post type from the cluster plan (pillar / listicle / migration / etc.)
2. Run `audit_post(post_path, post_type, product_context_path)` for each post, with `product_context_path` pointing to `product-context.md`
3. Generate the QA report at `qa-reports/[slug]-qa-report.md`
4. Return verdict: PASS / PASS WITH MINOR FIXES / FAIL

## Verdict logic

- **0 hard violations, 0–2 soft violations:** PASS — ship as-is
- **0 hard violations, 3+ soft violations:** PASS WITH MINOR FIXES — flag soft issues for the next revision but don't block
- **1+ hard violations:** FAIL — return to writer with the report; do not publish

## What this skill does NOT do

- Does not rewrite content
- Does not edit the original .md files
- Does not run on incomplete drafts (the writer should mark posts "ready for QA" before handoff)
- Does not check visual assets (that's the visual-designer skill's job)
- Does not check live links (that's the link-validator skill's job)
- Does not check published-post performance (that's the performance-tracker skill's job)

The skill's only output is the QA report. Everything else is handled by the next skill in the chain.

## Re-QA cycle

If the writer fixes hard violations and resubmits, the QA report from the second pass should reference the first pass:

```
**Audit cycle:** 2 of N
**Previous QA report:** qa-reports/[slug]-qa-report-v1.md
**Hard violations remaining from v1:** 0 (all fixed)
**New violations introduced:** 0
```

This pattern keeps an auditable trail of what changed between revisions.
