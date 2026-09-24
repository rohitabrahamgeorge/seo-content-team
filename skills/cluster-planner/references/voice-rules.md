# Voice Rules for Cluster Content (Reference)

This is a quick reference for cluster planning and writing. The full voice fingerprint lives in the brand voice skill named in product-context.md, if one is installed, otherwise the Voice section of product-context.md. Read that before drafting any prose. This file is a tactical checklist for the planner to make sure planned H1s, FAQ questions, and concession lines fit the voice before they reach the writer.

## Hard banned characters and patterns

- **No em dashes anywhere.** Replace with commas, periods, or parentheses.
- **No ampersands in body copy.** Use "and". Acceptable in product names if the product itself uses one.
- **No Title Case in headings.** Use sentence case. "How does {{PRODUCT}} compare to {Competitor}?" not "How Does {{PRODUCT}} Compare To {Competitor}?"
- **No ALL CAPS for emphasis.** Use bold sparingly instead.
- **No exclamation marks** except in CTAs (and even then, sparingly).
- **No "ultimate", "complete", "definitive"** as headline modifiers. They are overused and meaningless.
- **No "X best Y" without a number.** "The 7 best Mixpanel alternatives" is fine. "The best Mixpanel alternatives" is lazy.

## Voice characteristics

- **First person plural in pillars**: "we" ({{PRODUCT}} speaking)
- **First person singular in narrative posts**: "I" (guest author or named team member)
- **Second person imperative in tutorials**: "Go to the dashboard, click Settings"
- **Active voice dominant**: passive voice only when subject genuinely matters less than action
- **Short declarative sentences**: average 12-18 words; vary rhythm with occasional short ones (3-7 words)
- **Specific over generic**: "Acme serves {named customers from product-context.md} across {verified country count}" not "Acme serves enterprise clients globally". Use only the proof points listed in product-context.md.

## Brand voice tonal markers (from the voice owner; defaults below, override in product-context.md)

The voice owner named in product-context.md sets the tone. Unless product-context.md says otherwise, the voice is:
- Direct without being aggressive
- Confident without being arrogant
- Specific with numbers and outcomes
- Willing to concede competitor strengths (this is mandatory in concession lines)
- Allergic to corporate fluff and buzzword soup

If a sentence could appear in any SaaS marketing page, it's wrong for {{PRODUCT}}.

## Headline phrasing patterns to use

**Do use:**
- "In-depth: {{PRODUCT}} vs {Competitor}" (pillar H1, applying the first-mention rule in product-context.md)
- "How does {Competitor} compare to {{PRODUCT}}?" (FAQ heading)
- "When to choose {{PRODUCT}} vs {Competitor}" (decision section)
- "Migrate from {Competitor} to {{PRODUCT}}" (migration guide H1)
- "Why I switched from {Competitor} to {{PRODUCT}}" (narrative)
- Question-as-H2 for FAQ-aligned sections ("How does {product area} compare?", using a product area from product-context.md)

**Don't use:**
- "The Ultimate Guide to..." (banned modifier)
- "{Competitor} vs {{PRODUCT}}: Which Is Better?" (avoid "which is better" phrasing, too clickbait, hurts AEO)
- "10 Reasons to Switch..." (round-number listicles read AI-generated)
- Anything with em dashes or ampersands

## FAQ phrasing patterns

FAQ questions must be phrased exactly as a user would type them into Google or speak them to ChatGPT. Use the actual PAA from SERP research, not invented phrasings.

**Good:**
- "Does Globex have an API?"
- "How much does Globex cost?"
- "Can I migrate my data from Globex?"
- "Which is better for B2B SaaS, Globex or {Competitor}?"

**Bad:**
- "What are the API capabilities of Globex?" (too formal, not how people search)
- "Investigating the cost structure of Globex" (not a question at all)
- "About migrating from Globex" (statement, not question)

## Concession line patterns

Every pillar must contain 3+ concessions. Format:

- **"Consider keeping {Competitor} if..."** + specific scenario where competitor genuinely wins
- **"{Competitor} stands out for..."** + acknowledgment of competitor strength
- **"Prefer {feature unique to competitor}? {Competitor} is a solid choice."**

Concessions must be REAL. Don't manufacture fake concessions ("if you prefer worse software, choose them" is sarcasm, not a concession).

## Pricing phrasing

- Always show real numbers in tables. "Contact us" pricing is not used in comparison content.
- Use a 3-table pricing scenario block: low-volume case, typical case, high-volume case.
- If {{PRODUCT}} pricing is higher than competitor at a given volume, say so plainly. "At 100 users, Acme costs $X vs Globex's $Y. Acme is more expensive at this volume; the value comes when you need more than what Globex covers."

## "Good to know" callout pattern

Each feature comparison section ends with a green "Good to know" callout. Format:

> **Good to know:** [1-3 sentence concession, extra benefit, or counter-intuitive fact about {{PRODUCT}}'s approach to this feature area]

Examples:
> **Good to know:** Acme's reporting area doesn't include native scheduled exports yet. We integrate with two common BI tools for now, with native scheduling on the roadmap for next quarter.

> **Good to know:** Both Acme and Globex charge per-user. Acme's billing is based on monthly active users, not total registered users, which means dormant accounts don't increase your bill.

The pattern is: concede something OR add an unexpected benefit OR clarify a confusing point. Never use this slot for marketing copy. Real callouts must only state gaps, roadmap items and billing details that product-context.md lists.

## What to do when uncertain

If the planner produces an H1, FAQ question, or concession line and isn't sure whether it fits the voice, default to the simplest most direct phrasing. Read it aloud. If it sounds like a LinkedIn post from the voice owner named in product-context.md, it's probably right. If it sounds like a SaaS marketing page, rewrite.
