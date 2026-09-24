# PostHog Visual Pattern — Line-by-Line Audit

*Independent analysis of public pages. Not affiliated with PostHog.*

**Sources audited:**
- `https://posthog.com/blog/posthog-vs-mixpanel` (pillar)
- `https://posthog.com/blog/posthog-vs-amplitude` (pillar — second sample for verification)
- `https://posthog.com/blog/best-mixpanel-alternatives` (alternatives listicle)

Last verified: April 2026.

This file is the source of truth for what PostHog actually uses for visuals (versus what AI-trained patterns assume PostHog uses). Re-verify before each cluster run.

## Pillar visual inventory (verified, both pillars matched)

### Top of post (in order)
1. **Site header / nav** with PostHog brand mark
2. **Hero illustration** — full-width brand mascot illustration with brand background
3. **Hero image** — branded image with post title overlaid. Dimensions observed: 1920x1080 (Mixpanel) and 2400x1260 (Amplitude). Format: JPG via Cloudinary CDN.
4. **H1 title** (text, not in image)
5. **Author byline** with 2 avatars (50x50 circular crops), the same two named authors on both pillars
6. **Date** (text)
7. **Tag** "Comparisons" (text link)
8. **Auto-generated table of contents**

### Mid-post (after the "How is PostHog different?" section)
9. **Promotional CTA banner** — a promo banner with a short install headline, a one-line supporting sentence, a one-line install command, and a brand mascot illustration on the right. Two background textures (light and dark variants behind the banner).

### Body sections (the "Comparing PostHog and X" sections)
10. **Comparison tables only.** Zero images, zero infographics, zero diagrams in any individual feature section.
11. **"Good to know" callouts** are styled blockquotes with text only. No icons, no illustrations, no visual treatment beyond markdown blockquote styling.

### End of post (in order)
12. **Promotional CTA banner repeated** — same banner as mid-post, identical copy, identical illustration. Just repeated.
13. **FAQ section** — text only, no images, expandable accordions
14. **Newsletter subscribe banner** — a subscribe prompt with the newsletter name and a readership-size line, with a character illustration on the left. CTA button on the right.
15. **Boilerplate footer** (italic blockquote with all product links inline). No image.

## Listicle visual inventory (verified)

### Top of post (in order)
1. **Site header / nav**
2. **Hero illustration** (brand mascot)
3. Multiple promotional images stacked (action figure promo, screenshots — these appear to be ad placements PostHog inserts into all blog posts as a kind of brand merch promotion)
4. **Hero image** — branded with title "The most popular Mixpanel alternatives & competitors, compared". Dimensions: 1209x680. Format: JPG via Cloudinary.
5. **H1 title**
6. **Author byline** (same two named authors)
7. **Date**
8. **Tag** "Comparisons"
9. **Auto-generated TOC**

### Per main entry (4 main entries: PostHog, GA4, Amplitude, Heap)
10. **One product screenshot** per entry, placed immediately under the H2 entry heading. The screenshots are real product UIs:
    - PostHog: "hogflix-dashboard.png" — actual PostHog dashboard
    - GA4: "GA4.png" — actual GA4 interface
    - Amplitude: "ammplitude.png" (sic) — actual Amplitude dashboard
    - Heap: "heap.png" — actual Heap dashboard
11. **No other images per entry.** All comparison data is in tables and bullet lists. The "Bottom line" verdict is a styled blockquote, no image.

### Per honorable mention entry
12. **Zero images.** The honorable mentions section is bullet-list-only with no visual treatment.

### End of post
13. **Promotional CTA banner mid-post** (after "Which alternative should you choose?" section) — the same wizard banner
14. **"Is PostHog right for you?" section** — text only
15. **Promotional CTA banner repeated** — same banner again
16. **FAQ section** — text only
17. **Newsletter banner** — same as the pillar
18. **Boilerplate footer** — text only

## Pattern summary (the rules)

### Hero images
- Always 1 per post, at the top
- Full-width
- Illustrated, not photographic
- Title overlaid on the image (the H1 also appears below as text)
- Brand-consistent (PostHog uses their illustrated character + brand colors throughout)

### In-body images
- Pillars: zero in-body images
- Listicles: one product screenshot per main entry
- Migration guides: PostHog doesn't have one to audit; default to pillar pattern (zero in-body images)

### Author avatars
- 50x50 circular crops
- One per author (PostHog uses 2 authors per post; could be 1 for {{PRODUCT}})

### CTA banners
- Same banner repeated 2x (mid-post + end-post)
- Branded, illustrated, with background texture
- Single CTA action (a one-command install → wizard download)
- For {{PRODUCT}}: if the buying motion is sales-led rather than self-serve, the equivalent is "Request a demo"; if self-serve, use the signup or install action

### Newsletter banner
- 1 per post, at the end (above the boilerplate footer)
- Branded illustration on the left
- Subscribe form on the right

### Texture / background images
- Used as banner backgrounds, not as standalone visuals
- Two variants (light + dark) for the CTA banner

## What PostHog deliberately does NOT do

Each of these is a temptation that AI-trained patterns might suggest. PostHog rejects all of them:

- ❌ **No comparison infographics** — comparison tables do all the work
- ❌ **No "X vs Y" Venn diagrams** — none anywhere
- ❌ **No decision flowcharts** — recommendations are text bullet lists
- ❌ **No feature-grid visualizations** — feature lists are bullets
- ❌ **No section divider images** — sections are demarcated by H2/H3 headings only
- ❌ **No icons next to "Good to know" callouts** — pure styled blockquotes
- ❌ **No animated GIFs** — none anywhere
- ❌ **No mid-post charts or bar graphs** — even the pricing comparison is a table, not a chart
- ❌ **No section-header illustrations** — clean text headings only
- ❌ **No screenshots of competitor products in the pillar** — only in the listicle, where each main entry gets one
- ❌ **No product demo videos embedded inline** — videos linked out, not embedded

This minimalism is deliberate. PostHog's content is text-first. Visuals appear only where they're load-bearing: the hero (sets brand context), the listicle screenshots (visual identification of each tool), and the CTA/newsletter banners (conversion).

## Why this matters for the cluster visual designer

The visual designer skill must follow this minimalism. The temptation to add custom infographics, decision trees, or Venn diagrams is real because those visuals would look impressive. But PostHog's data shows minimal visuals work — their pages rank, get cited by AI engines, and convert. Adding more visuals would slow the page, distract the reader, and signal to search engines that the post is more decorated than informative.

For a {{PRODUCT}} 8-post cluster, the visual workload is:
- 8 hero images (1 per post) — unique
- 4 product screenshots/representations (only on the listicle) — unique to that post
- 1 CTA banner — shared across all 8 posts
- 1 newsletter/podcast banner — shared across all 8 posts
- 1 author avatar — shared across all 8 posts

That's 12 unique image assets + 3 shared assets = 15 total assets for the entire cluster. That's significantly less than what an "AI infographic generator" approach would produce, and it matches what's actually proven to work.

## Re-verification trigger

Re-fetch and re-audit when:
- A new cluster is being planned (re-check that PostHog hasn't changed their pattern)
- More than 60 days have passed since the last verification
- {{PRODUCT}}'s brand assets have visibly changed on {{WEBSITE}} (then update the Visual identity note in product-context.md and re-derive the {{PRODUCT}}-specific design direction)
