---
name: cluster-visual-designer
description: Designs the small, specific set of visuals that a PostHog-style cluster post actually needs. Use this skill when the orchestrator hands off a written post (or set of posts) for visual treatment. Triggers on phrases like "design the visuals for the pillar", "generate the hero image", "create the CTA banner for this post", "visualize the cluster post", or any handoff from cluster-writer with a complete post. This skill follows PostHog's verified visual pattern (1 hero image per post, 1 product screenshot per main listicle entry, 2 repeated CTA promo banners, 1 newsletter banner). It does NOT produce infographics, decision trees, comparison flowcharts, or feature-section diagrams because PostHog does not. The output is SVG specs and image briefs that can be handed to a designer or rendered programmatically.
---

# Cluster Visual Designer

This skill produces the visuals needed for a {{PRODUCT}} competitor-cluster post. The visual treatment is deliberately minimal because that's what PostHog actually does, and the cluster system follows PostHog's pattern verbatim.

## When to use this skill

Trigger when:
- The cluster-writer skill has produced a finished post and the orchestrator handed it off for visual treatment
- A user asks for "visuals for the pillar" or "design the hero for this post"
- The cluster-orchestrator's state file shows a post in `status: written` with `visuals: pending`

Required inputs:
- Finished post markdown file (the writer's output)
- Post type (pillar / listicle / migration / narrative / pricing / 3-way / review / category)
- Cluster name (e.g. `{{PRODUCT_SLUG}}-vs-{competitor}`) for asset naming
- `product-context.md` (at the working-folder root, next to `clusters/`) for brand name, website and the Visual identity note
- Brand assets folder if available (logo, color palette, fonts)

If any of these are missing, halt and ask the orchestrator.

## What PostHog actually does (verified)

Verified by direct fetches of three PostHog pillars (vs Mixpanel, vs Amplitude) and one alternatives listicle (best Mixpanel alternatives) on April 2026.

### Pillar visual inventory
1. **1 hero image** at the top — full-width, illustrated, brand-consistent (PostHog uses a brand mascot illustration + product name overlay)
2. **1 author avatar per author** in the byline (small circular thumbnails)
3. **0 in-body images for individual sections** — no infographics, no diagrams, no flowcharts
4. **2 identical promotional CTA banners** — one mid-post (after the differentiators), one at the end (after recommendations). Same banner, same copy, repeated.
5. **1 newsletter subscribe banner** at the end (above the boilerplate footer)

### Listicle visual inventory
1. **1 hero image** at the top
2. **1 product screenshot per main entry** (4 screenshots for a 4-entry listicle)
3. **0 images** for honorable mentions
4. **2 identical promotional CTA banners**
5. **1 newsletter banner**

### Migration guide visual inventory
1. **1 hero image** at the top
2. **0 in-body images** (PostHog doesn't have a migration guide in the Mixpanel cluster, so this is an inference — match the pillar pattern)
3. **1 promotional CTA banner** at the end

### Critical: what PostHog does NOT do

- ❌ No comparison infographics
- ❌ No "X vs Y" Venn diagrams
- ❌ No decision flowcharts
- ❌ No feature-grid visualizations
- ❌ No animated GIFs
- ❌ No mid-post charts or graphs
- ❌ No section divider images
- ❌ No "Good to know" callout illustrations

If you find yourself wanting to design any of the above, stop. PostHog's pattern is to let comparison tables do the work in text, and the absence of in-body visuals is a deliberate design choice that keeps the page light, fast, and reads-naturally.

## The 5 visual outputs to produce per cluster

### Output 1: Hero image (1 per post, 8 per cluster)

**Specification:**
- Aspect ratio: 16:9 (PostHog uses 1920x1080 → 2400x1260 depending on post)
- Style: Illustrated, not photographic
- Composition: Brand element on the left or center, post title overlay on the right
- Background: Solid brand color or subtle gradient
- Format: SVG preferred (vector, scalable); PNG or JPG fallback
- Filename: `hero-[post-slug].svg`

**{{PRODUCT}}-specific design direction:**
- If the Visual identity note in `product-context.md` names a brand mascot or illustration style, use it. If not, do not invent a mascot. Use abstract geometric forms, icons for the product's core capabilities (taken from Verified capabilities in `product-context.md`), or layered shapes representing the product's category line from Positioning
- Color palette: use the brand colours and logo rules in `product-context.md` (add a Visual identity note there if needed), then confirm against the live {{WEBSITE}} homepage. If no colours are recorded yet, start from these neutral defaults and override them: background `#0F172A`, text `#FFFFFF`, accent `#3B82F6`
- Typography: Match {{WEBSITE}} headline typography (verify on each run; do not assume)
- Title overlay: The post H1 in clean sans-serif, max 2 lines

**Hero image content per post type:**

| Post type | Hero composition |
|---|---|
| Pillar | Two illustrated platform "blocks" facing each other ({{PRODUCT}} vs Competitor) |
| Alternatives listicle | Stacked tier of 4 platform "blocks" representing the alternatives ranked |
| Migration guide | Arrow-and-pathway illustration showing movement from competitor to {{PRODUCT}} |
| Narrative | Single founder-style illustration with a quote-mark element |
| Pricing explainer | Calculator-or-receipt illustration with currency symbols |
| 3-way comparison | Three platform blocks in triangular arrangement |
| B2B review | Magnifying glass over a platform block, "audit"-style visual |
| Category explainer | Two distinct categories side-by-side, with the difference highlighted |

### Output 2: Product screenshot or representation (1 per main listicle entry, 4 per listicle)

**Specification:**
- Aspect ratio: 16:9 or 4:3
- Style: Real product UI screenshot if available; clean dashboard illustration if not
- Format: PNG (UI screenshots typically don't vectorize well)
- Filename: `screenshot-[entry-slug].png`

**{{PRODUCT}}-specific design direction:**
- For the {{PRODUCT}} entry: Use a real screenshot of the {{PRODUCT}} dashboard or a core product view. If unavailable, use an illustrated representation matching {{WEBSITE}}'s design language
- For competitor entries (for example, Globex): Do NOT use real screenshots from competitor sites (copyright, brand-confusion, and image-hotlinking issues). Instead, use clean illustrated representations or generic dashboard mockups
- All screenshots/representations should be visually consistent in style (e.g. all illustrated, OR all real screenshots — never mixed)

### Output 3: Primary CTA banner (1 banner used in 2 places per pillar/listicle)

This is the "Install PostHog with one command" equivalent. It appears mid-post and end-post — same banner, same copy.

**Specification:**
- Aspect ratio: ~3:1 (wide banner)
- Style: Branded background with subtle texture, large CTA element on the right, illustrated brand element on the left
- Format: SVG with linked PNG illustrations
- Filename: `cta-primary-[cluster-name].svg` (one banner reused across all posts in the cluster)

**{{PRODUCT}} CTA banner content:**

```
[Background: brand color with subtle texture]
[Left: small {{PRODUCT}} brand mark / illustrated element]

Headline: "[Primary offer, under 10 words, drawn from the core positioning statement]"
Subhead: "[One line on the next step, using only offers listed in product-context.md]"

[Right: CTA button "[Primary action, e.g. Request a demo or Start free]" → {{WEBSITE}}/[cta-path]]
```

The PostHog version uses a one-line npm install command. If {{PRODUCT}} has no self-serve install equivalent (a sales-led motion), the CTA is "Request a demo." If it is self-serve, use the signup or install action. Match the visual rhythm but adapt the offer.

### Output 4: Author byline with avatar(s)

**Specification:**
- Avatar: 50x50 circular crop
- Format: PNG or JPG
- Filename: `author-[name].jpg`

For posts authored by "{{PRODUCT}} Team" (the placeholder used in cluster-writer output), use a single team avatar: either a {{PRODUCT}} logo mark in a circle, or a stylized monogram of the product initials.

When real human authors are assigned (e.g. the voice owner named in product-context.md for some thought-leadership pieces), use a real photo headshot.

### Output 5: Newsletter / footer banner (1 per cluster, reused across all posts)

**Specification:**
- Aspect ratio: ~3:1
- Format: SVG
- Filename: `newsletter-[cluster-name].svg`

PostHog's version is a subscribe prompt with the newsletter name and a readership-size line. {{PRODUCT}}'s equivalent should match {{PRODUCT}}'s actual content distribution channel (newsletter, podcast, community or similar). Check {{WEBSITE}} and `product-context.md` before each cluster run for the current newsletter or distribution offer. Only use a readership or audience number if it appears in Verified proof points.

```
[Left: newsletter or podcast cover art, or {{PRODUCT}} brand mark]

Headline: "[Name of the newsletter, podcast or channel]"
Subhead: "[What it covers and how often, in one line]"

[Right: CTA button "[Subscribe / Listen now / Join]" → {{WEBSITE}}/[channel-path]]
```

## Per-post visual brief output format

For each post in a cluster, output a brief in this format:

```yaml
post: acme-vs-globex
post_type: pillar
visuals_required:
  - id: hero
    spec: 2400x1260 SVG, two-platform-blocks composition
    title_overlay: "In-depth: Acme vs Globex"
    filename: hero-acme-vs-globex.svg
    status: pending
  - id: cta-banner
    spec: shared cluster CTA banner (Output 3)
    placement: after differentiators, after recommendations
    filename: cta-primary-acme-vs-globex.svg
    status: shared-asset
  - id: newsletter-banner
    spec: shared cluster newsletter banner (Output 5)
    placement: end of post
    filename: newsletter-acme-vs-globex.svg
    status: shared-asset
  - id: author-avatar
    spec: 50x50 Acme team avatar
    filename: author-acme-team.jpg
    status: shared-asset
total_unique_visuals_needed: 1 (hero only)
total_shared_assets: 3 (CTA, newsletter, author)
```

## Process

1. **Read the post.** Open the markdown file and extract the H1, post type, and any specific visual cues from the body (e.g. the listicle has 4 numbered entries → 4 product screenshots needed).

2. **Check shared assets.** Look in `images/shared/` for existing CTA banner, newsletter banner, and author avatar. If they exist for this cluster, reuse them. If not, generate them as part of this run (one-time per cluster).

3. **Generate the per-post visual brief** following the YAML format above.

4. **Generate SVG specifications** for each unique visual. The specifications should be detailed enough that a designer (or a follow-up SVG generation step) can produce the actual image. Specs include: dimensions, color codes (hex), text content, font family, layout coordinates, brand element positions.

5. **Render where possible.** If the orchestrator has access to image-generation tools (Midjourney, DALL-E, the Visualizer, or programmatic SVG generation), render the visuals. Otherwise, hand off the brief to a designer.

6. **Save outputs.** Place rendered visuals in `images/[post-slug]/` and shared assets in `images/shared/`. Update the post's frontmatter with image paths.

## Brand asset verification

Before each cluster run, fetch {{WEBSITE}} and verify:
- Current brand color palette (extract from CSS or homepage)
- Current typography (headline font and body font)
- Current logo asset (download fresh)
- Current customer logos (in case they've changed since the last run)
- Current newsletter or podcast art and tagline (for the newsletter banner)

Record anything that changed in the Visual identity note of `product-context.md`. Do not cache brand assets across clusters. The brand may evolve; each cluster's visuals should reflect the current {{WEBSITE}}.

## Quality checklist

Before declaring visuals complete:

- [ ] Hero image generated for every post in the cluster (8 unique heroes for an 8-post cluster)
- [ ] CTA banner is a single shared asset (not 8 different banners)
- [ ] Newsletter banner is a single shared asset
- [ ] Author avatar is a single shared asset
- [ ] Listicle has 4 product screenshots/representations (one per main entry; honorable mentions get none)
- [ ] No infographics, no comparison diagrams, no decision flowcharts (the temptation is real; resist it)
- [ ] All filenames follow the `[type]-[slug].svg|png|jpg` convention
- [ ] Image paths added to each post's frontmatter as a `hero_image:` field and (for listicles) a `screenshots:` field
- [ ] Shared assets stored in `images/shared/` so they don't get duplicated

## Failure modes to avoid

1. **Designing infographics.** PostHog doesn't. Don't.
2. **Generating different CTA banners for each post.** PostHog uses one banner repeated. So should {{PRODUCT}}.
3. **Hotlinking competitor product screenshots.** Use illustrated representations or generic dashboard mockups instead.
4. **Generating images that don't match {{WEBSITE}}'s brand.** Verify brand assets fresh before each cluster.
5. **Adding "Good to know" callout icons or section divider images.** PostHog doesn't have these. Don't add them.
6. **Generating decorative images that don't earn their place.** If the post reads fine without an image at a given location, don't add one.

## What this skill does not do

- It does not generate the actual SVG/PNG bitmap files end-to-end. It produces specifications and briefs that downstream tools (the Visualizer, Midjourney, or a designer) render.
- It does not pick brand colors or fonts. It reads them from `product-context.md` and {{WEBSITE}}.
- It does not write image alt text. That's the QA skill's job.
- It does not optimize images for web delivery (compression, CDN). That's the publishing pipeline's job.

## Reference reads required

- `references/posthog-visual-pattern.md` (this folder) — line-by-line audit of what PostHog actually uses for visuals
- `https://posthog.com/blog/posthog-vs-mixpanel` and `https://posthog.com/blog/best-mixpanel-alternatives` — re-fetch live to catch any drift
- `{{WEBSITE}}` — verify current brand assets
- `product-context.md` — brand name, voice owner, proof points and the Visual identity note
