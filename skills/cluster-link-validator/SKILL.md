---
name: cluster-link-validator
description: Validates the internal link topology of a content cluster before publish. Confirms every spoke links back to the pillar, the pillar links to every spoke, anchor text is descriptive and keyword-rich, and no external competitor links have leaked through. Use this skill after the writer has produced posts and the quality-checker has passed them, but before publish. Trigger on phrases like "validate links", "check the link topology", "audit cluster links", "is the cluster bidirectional", "are there any broken internal links", or any handoff that asks for the link-level gate before publishing. This skill produces a pass/fail report and does not edit content. The blog manager or writer fixes any issues before publish.
---

# Cluster Link Validator

This is the final structural gate before publish. The quality-checker handles content rules. The link-validator handles topology: are the posts wired together correctly? PostHog's hub-and-spoke pattern only works when every spoke links back to the pillar with descriptive anchor text and no link equity leaks to competitor sites.

## When to use this skill

Trigger when:
- The writer has produced all posts in a cluster
- The quality-checker has passed them (PASS or PASS WITH MINOR FIXES)
- The visual-designer has finished (so the SVG/image alt-text links can be checked too)
- Someone asks "are the links wired up correctly", "validate the cluster", "check link topology"
- Before any publish action — this is the last gate

Required inputs:
- Cluster folder path
- All posts in `posts/*.md`
- The cluster plan (to know which posts are expected to exist and how they should cross-link)
- `product-context.md` at the root of the working folder (next to `clusters/`), for the product areas and their page URLs (so internal links to product pages can be verified) and the Competitor set (so competitor domains can be flagged)

If posts are missing or the plan is incomplete, halt and ask the orchestrator to confirm scope.

## What the link validator outputs

A single report at `clusters/[cluster-name]/link-validation-report.md` with this structure:

```markdown
# Link Validation Report: [cluster name]

**Date:** [today]
**Posts validated:** [count]
**Verdict:** PASS | PASS WITH WARNINGS | FAIL

## Summary

- Bidirectional pillar-spoke links: [count] of [expected] complete
- Cross-spoke links: [count] of recommended
- Anchor text quality: [pass/fail per post]
- External competitor links found: [count]
- Broken or placeholder internal links: [count]
- Outbound product hub links: [coverage report]

## Hard violations (block publish)

[Listed by severity]

## Warnings (recommended fixes, not blocking)

[Listed by severity]

## What passed

[Quick affirmation of the rules that did pass]

## Recommended next action

[Either "Ship as-is", "Writer fixes hard violations then re-validate", or "Revise comprehensively"]
```

## The 9 link-level rules

### Rule L1: Pillar links to every spoke (HARD)

For each non-pillar post in the cluster's published or planned scope, confirm the pillar contains an internal link to it. Use the slug as the matcher.

**Check:**
```python
pillar_content = open('posts/[pillar-slug].md').read()
for spoke in cluster_state['spokes']:
    expected_link = f"/blog/{spoke['slug']}"
    if expected_link not in pillar_content:
        violation: pillar missing link to spoke
```

**Severity:** HARD (cluster fails its primary topology test if pillar is missing spoke links).

**Fix:** Writer adds a link in the body or in Related Reading, with descriptive anchor text matching the spoke's primary keyword.

### Rule L2: Every spoke links back to the pillar (HARD)

For each non-pillar post, confirm at least one internal link to the pillar exists in the body (not just Related Reading — the body link signals topical hierarchy more strongly).

**Check:**
```python
pillar_url = f"/blog/{pillar_slug}"
for spoke_path in spoke_paths:
    body = strip_related_reading(open(spoke_path).read())
    if pillar_url not in body:
        violation: spoke missing body link to pillar
```

**Severity:** HARD.

**Fix:** Writer inserts a body link to the pillar with anchor text that matches the comparison subject (e.g. "{{PRODUCT}} vs Globex" not "click here").

### Rule L3: No external links to competitor websites (HARD, redundant with QA Rule 3 but checked again)

Match all `https?://` URLs in markdown links. Flag any where the domain matches a competitor in the Competitor set of product-context.md. This is checked again here because (a) sometimes competitor URLs slip in via image alt text or table cells the QA grep missed, and (b) the writer might add a link in revision after QA already passed.

**Severity:** HARD.

**Fix:** Replace with internal {{PRODUCT}} link or remove the link.

### Rule L4: No broken or placeholder internal links (HARD)

Every internal link `/blog/[slug]` must point to a slug that exists in the cluster plan or in another live cluster. Match each internal link's slug against `cluster-state.json`'s known slugs and against the master cluster index in the Google Sheet (if available).

**Check:**
```python
for post in posts:
    for link in extract_internal_links(post):
        if link.startswith('/blog/'):
            slug = link.split('/blog/')[1].rstrip('/')
            if slug not in known_slugs:
                violation: post links to /blog/{slug} which doesn't exist
```

**Severity:** HARD if pre-publish; SOFT if the missing slug is in the cluster plan and scheduled to publish in the same wave.

**Fix:** Either remove the placeholder link, or confirm the linked spoke is publishing in the same wave (Wave 1 co-launch covers pillar + listicle + pricing; Wave 2 covers the rest).

### Rule L5: Anchor text is descriptive (SOFT)

For each internal link, check the anchor text against a descriptive-anchor heuristic:

- ❌ "click here", "learn more", "read more", "this post" (alone)
- ❌ Bare URLs as anchor
- ✓ Keyword-rich phrases ("Best Globex alternatives", "{{PRODUCT}} vs Globex comparison")
- ✓ Contextual descriptors that include at least one entity ("our full breakdown", "the migration guide")

**Severity:** SOFT (the cluster works without perfect anchors, but they help SEO).

**Fix:** Update the anchor text to include the linked post's primary keyword or a descriptive phrase.

### Rule L6: No duplicate-anchor-different-target violations (HARD)

If the same anchor text appears on the same page linking to two different URLs, Google reads this as keyword stuffing.

**Check:** For each post, build a map of `anchor_text → set(target_urls)`. Flag any anchor with more than one target.

**Severity:** HARD.

**Fix:** Differentiate the anchors (e.g. "Globex pricing" → "Globex pricing breakdown" for the second instance).

### Rule L7: Pillar links to all product area pages listed in product-context.md (SOFT)

The boilerplate footer should include links to every product area page listed in product-context.md (whatever the current product list is, verified against that file). On listicles and pillars, these links should also appear inline at least once in the body.

**Hub pages (taken from product-context.md, for example):**
- `{{WEBSITE}}/` (main)
- `{{WEBSITE}}/{product-area-1}`
- `{{WEBSITE}}/{product-area-2}`
- `{{WEBSITE}}/blog`
- `{{WEBSITE}}/pricing`
- `{{WEBSITE}}/contact`

(If product-context.md expands or changes the product area list, update the rule.)

**Severity:** SOFT.

**Fix:** Add missing product hub links in boilerplate or body.

### Rule L8: No orphan posts (HARD)

Every spoke must have at least one inbound internal link from another post in the cluster (the pillar counts). If a spoke has zero inbound links, it's orphaned and won't get crawl priority.

**Check:**
```python
for spoke in spokes:
    inbound = sum(
        1 for other_post in all_posts
        if other_post != spoke
        and f"/blog/{spoke['slug']}" in open(other_post).read()
    )
    if inbound == 0:
        violation: spoke is orphaned
```

**Severity:** HARD.

**Fix:** Add an inbound link from the pillar at minimum, ideally from 2+ siblings.

### Rule L9: Knowledge base references use correct anchor format (SOFT)

When the migration guide or other technical post references the product's help center or knowledge base (`{{DOCS_URL}}`, from product-context.md), the anchor should match a descriptive pattern, not a bare URL.

**Severity:** SOFT.

**Fix:** Replace bare KB URL with descriptive anchor like "{{PRODUCT}} Knowledge Base" or "see the migration documentation".

## The validation script

```python
import re
import os
import json
from pathlib import Path

# Fill from the Competitor set in product-context.md (one entry per competitor domain)
COMPETITOR_DOMAINS = [
    "globex.example", "initech.example", "umbrella.example",
    "hooli.example", "soylent.example", "vandelay.example"
]

# Fill from the product area pages listed in product-context.md
PRODUCT_HUB_PAGES = [
    "{{WEBSITE}}/",
    "{{WEBSITE}}/{product-area-1}",
    "{{WEBSITE}}/{product-area-2}",
    "{{WEBSITE}}/blog",
    "{{WEBSITE}}/pricing",
    "{{WEBSITE}}/contact"
]

WEAK_ANCHORS = ["click here", "learn more", "read more", "this post", "here", "this"]

def extract_links(content):
    """Returns list of (anchor, url, line_number) tuples."""
    links = []
    for i, line in enumerate(content.split("\n")):
        for match in re.finditer(r"\[([^\]]+)\]\(([^\)]+)\)", line):
            links.append((match.group(1), match.group(2), i + 1))
    return links

def strip_related_reading(content):
    """Remove the Related Reading section so we check body links only."""
    parts = re.split(r"###?\s*Related reading", content, flags=re.IGNORECASE)
    return parts[0] if parts else content

def validate_cluster(cluster_path):
    state = json.load(open(f"{cluster_path}/cluster-state.json"))
    pillar_slug = next(p['slug'] for p in state['posts'] if p['type'] == 'pillar')
    
    posts = {}
    for post_meta in state['posts']:
        if post_meta.get('status') in ('written', 'published'):
            path = f"{cluster_path}/posts/{post_meta['slug']}.md"
            if os.path.exists(path):
                posts[post_meta['slug']] = {
                    'meta': post_meta,
                    'content': open(path).read()
                }
    
    pillar_content = posts[pillar_slug]['content']
    violations = {"hard": [], "soft": []}
    
    # L1: pillar links to every spoke
    for slug, post in posts.items():
        if slug == pillar_slug:
            continue
        if f"/blog/{slug}" not in pillar_content:
            violations["hard"].append({
                "rule": "L1",
                "post": pillar_slug,
                "issue": f"Pillar missing link to spoke /blog/{slug}",
                "fix": f"Add internal link to /blog/{slug} with descriptive anchor."
            })
    
    # L2: every spoke links back to pillar (in body, not just Related Reading)
    pillar_url = f"/blog/{pillar_slug}"
    for slug, post in posts.items():
        if slug == pillar_slug:
            continue
        body = strip_related_reading(post['content'])
        if pillar_url not in body:
            violations["hard"].append({
                "rule": "L2",
                "post": slug,
                "issue": f"Spoke missing body link to pillar {pillar_url}",
                "fix": f"Add inline body link to {pillar_url} with anchor matching comparison subject."
            })
    
    # L3: no external competitor links
    for slug, post in posts.items():
        for anchor, url, line in extract_links(post['content']):
            for domain in COMPETITOR_DOMAINS:
                if domain in url:
                    violations["hard"].append({
                        "rule": "L3",
                        "post": slug,
                        "line": line,
                        "issue": f"External link to competitor {domain}: [{anchor}]({url})",
                        "fix": f"Either unlink '{anchor}' or replace with internal {{PRODUCT}} link."
                    })
    
    # L4: no broken internal links
    known_slugs = set(p['slug'] for p in state['posts'])
    for slug, post in posts.items():
        for anchor, url, line in extract_links(post['content']):
            if url.startswith('/blog/'):
                target_slug = url.split('/blog/')[1].rstrip('/')
                if target_slug not in known_slugs:
                    severity = "hard" if state.get('publish_status') == 'pre-publish' else "soft"
                    violations[severity].append({
                        "rule": "L4",
                        "post": slug,
                        "line": line,
                        "issue": f"Internal link to /blog/{target_slug} (slug not in cluster plan)",
                        "fix": "Remove placeholder link or confirm the spoke is being added to the plan."
                    })
    
    # L5: anchor text quality
    for slug, post in posts.items():
        for anchor, url, line in extract_links(post['content']):
            if anchor.lower().strip() in WEAK_ANCHORS:
                violations["soft"].append({
                    "rule": "L5",
                    "post": slug,
                    "line": line,
                    "issue": f"Weak anchor text: '{anchor}'",
                    "fix": "Update anchor to include the linked post's primary keyword."
                })
    
    # L6: no duplicate-anchor-different-target
    for slug, post in posts.items():
        anchor_targets = {}
        for anchor, url, line in extract_links(post['content']):
            anchor_targets.setdefault(anchor, set()).add(url)
        for anchor, targets in anchor_targets.items():
            if len(targets) > 1:
                violations["hard"].append({
                    "rule": "L6",
                    "post": slug,
                    "issue": f"Anchor '{anchor}' links to multiple URLs: {list(targets)}",
                    "fix": "Differentiate anchor text per target."
                })
    
    # L7: pillar links to product hubs (SOFT)
    pillar_hub_links = sum(1 for hub in PRODUCT_HUB_PAGES if hub in pillar_content)
    if pillar_hub_links < 2:
        violations["soft"].append({
            "rule": "L7",
            "post": pillar_slug,
            "issue": f"Pillar links to only {pillar_hub_links} product hub pages",
            "fix": "Add product hub links in boilerplate footer and at least one body section."
        })
    
    # L8: no orphan posts
    for slug in posts:
        if slug == pillar_slug:
            continue
        inbound = sum(
            1 for other_slug, other in posts.items()
            if other_slug != slug
            and f"/blog/{slug}" in other['content']
        )
        if inbound == 0:
            violations["hard"].append({
                "rule": "L8",
                "post": slug,
                "issue": "Spoke has zero inbound internal links (orphaned)",
                "fix": "Add an inbound link from the pillar at minimum."
            })
    
    return violations

def write_report(cluster_path, violations):
    report_path = f"{cluster_path}/link-validation-report.md"
    verdict = "PASS" if not violations["hard"] else (
        "PASS WITH WARNINGS" if len(violations["hard"]) == 0 and violations["soft"]
        else "FAIL"
    )
    # Build markdown report (see template above)
    # ... (full template implementation)
    pass
```

## Verdict logic

- **0 hard violations, 0–3 soft warnings:** PASS — ship as-is
- **0 hard violations, 4+ soft warnings:** PASS WITH WARNINGS — flag for next iteration but don't block
- **1+ hard violations:** FAIL — return to writer with the report; do not publish

## Re-validation cycle

If the writer fixes hard violations and resubmits, the link-validator runs again. The report should reference the previous run:

```
**Validation cycle:** 2 of N
**Previous report:** link-validation-report-v1.md
**Hard violations remaining from v1:** 0 (all fixed)
**New violations introduced:** 0
```

## What this skill does NOT do

- Does not edit content (writer's job)
- Does not check schema markup (QA's job)
- Does not check live URLs work after publish (performance-tracker's job)
- Does not check images or visual asset links (visual-designer's job)
- Does not validate cross-cluster links (only within the current cluster's scope)
- Does not auto-fix anything

## Sample call

```bash
cd clusters/acme-vs-globex

# All posts written, QA passed, visuals done
python -m link_validator.run --cluster acme-vs-globex
```

Output is `clusters/acme-vs-globex/link-validation-report.md`. Verdict is the gate to publish.

## How this fits in the system

```
Writer → Quality Checker → Visual Designer → Link Validator → Blog Manager (publish)
                                                    ↑
                                          THIS SKILL is the last
                                          structural gate before
                                          a human ships the post
```

If link validation fails, the writer addresses it before the post goes live. Once it passes, the blog manager can publish with confidence that the cluster is wired up correctly.
