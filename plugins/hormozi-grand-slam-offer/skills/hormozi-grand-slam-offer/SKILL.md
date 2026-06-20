---
name: hormozi-grand-slam-offer
description: Craft or critique a "Grand Slam Offer" using Alex Hormozi's frameworks from $100M Offers — the Value Equation (Dream Outcome × Likelihood / Time × Effort), the 5-step Value Stack, pricing, guarantees (4 types), scarcity, urgency, bonuses, and MAGIC naming. Use this whenever someone wants to build, price, name, review, or improve any offer for a product, service, course, coaching program, agency engagement, or info product. Trigger on phrases like "help me craft an offer," "build my offer," "what should I charge," "review my offer," "critique this pricing," "make my offer irresistible," "value stack," "value equation," "Grand Slam offer," "guarantee for my product," "name my course/program/service," or "Hormozi" — even when the user doesn't say "offer" explicitly but is clearly trying to package and sell something. The skill produces a self-contained interactive HTML artifact with every section of the offer editable in-place.
---

# Hormozi Grand Slam Offer

A skill for crafting and critiquing offers using the frameworks from Alex Hormozi's *$100M Offers*. Every recommendation in this skill is grounded in the book — when in doubt, side with the book rather than your own intuition.

## What this skill actually does

There are two modes. Figure out which one the user is in within the first turn and tell them which mode you've picked so they can correct you.

**Craft mode** — the user has a product, service, or idea and wants to package it as a Grand Slam Offer. They might say "help me build an offer for my SaaS" or "I want to launch a coaching program but I don't know what to put in it." Walk them through the 5-step Value Stack process and the four enhancers (scarcity, urgency, bonuses, guarantees, naming). Output: a complete offer in the HTML artifact.

**Critique mode** — the user already has an offer (a landing page, a pricing PDF, copy they've written) and wants you to score it and suggest fixes. They might say "review my offer," "is this pricing too low," "what's wrong with this landing page?" Score each Value Equation dimension 0-1 (per Hormozi's Meditation-vs-Xanax table), flag the offer-quality gaps, and produce the HTML artifact in critique form — with the original on the left and your rewrite on the right.

**Both modes end in the same artifact.** Don't skip the artifact step. Even if the user only asks a narrow question ("what guarantee should I offer?"), if they're working on an offer, produce or update the artifact at the end.

## The core workflow

### Step 1 — Establish the foundation (don't skip this)

Before stacking anything, you need to know:

1. **Who is the avatar?** Specific — "busy moms in Towson, MD with kids 6-12" not "moms." If too generic, push back gently and ask for more. The book is brutal on this: "Don't make me niche slap you. The riches are in the niches." Apply the four market indicators (Pain, Purchasing Power, Easy to Target, Growing). If the market is clearly bad/dying, *say so* — no offer fixes a dying market.

2. **What is the dream outcome?** Not the deliverable — the *result*. "Lose 20 lbs in 6 weeks," not "weekly workout videos." If the user gives you a deliverable, ask "what does that get them in their life?" until you hit a real outcome. Frame in terms of status gained from others' viewpoint when possible (per Hormozi's golf-club example).

3. **What is the current price (or what does the user think they should charge)?** This anchors the conversation. If they say something like "$97/month for a course," you already know to push them on whether the perceived value justifies it — almost always the answer is "raise it."

You can ask these in one batch if the user is in a hurry, or one at a time if they want hand-holding. Skip questions where the answer is already in the conversation.

### Step 2 — Run the 5-step Value Stack process

This is the heart of the book. Read `references/value-stack-process.md` for the exact procedure. Briefly:

1. **Dream Outcome** — already captured in Step 1.
2. **List problems** — Every obstacle the prospect will hit, *in the order they hit them.* Hormozi insists you "think about it in insane detail." For each step of the customer journey, brainstorm problems that map to one of the four Value Equation buckets (Dream Outcome / Likelihood / Time / Effort). Aim for 20+ problems across 4-8 journey stages.
3. **Reframe as solutions** — Turn every problem into "How to [solve X] so that [benefit]." Use the language patterns in `references/value-stack-process.md`.
4. **Generate delivery vehicles** — For each solution, apply the Delivery Cube (1-on-1 vs group vs one-to-many; DIY vs DWY vs DFY; etc.) and the 10x/1/10x test. You want a *long* list — most will be cut.
5. **Trim and stack** — Sort by cost-to-deliver vs. value-to-customer. Keep low-cost-high-value and high-cost-high-value; cut the rest. Bundle the survivors. Name each bundle with the MAGIC formula. Assign a dollar value to each bundle.

The output of Step 2 is a list of named bundles, each with a description, a "this means you can…" benefit, and a dollar value. The bundles should sum to at least 10x the proposed price — that's the "money at a discount" lever.

### Step 3 — Apply the enhancers

Walk the four levers in this order — it's the order the book uses, and the order matters because each builds on the last:

1. **Guarantee** (`references/guarantees.md`) — Pick one of the four types (unconditional, conditional, anti-, implied) based on margin and ticket size. Write it as "If you don't X in Y, we will Z" — never weaker. Give it a memorable name.
2. **Scarcity** (`references/scarcity-urgency.md`) — Quantity-based. Pick total cap, growth-rate cap, or cohort cap. Be honest about real capacity.
3. **Urgency** (`references/scarcity-urgency.md`) — Time-based. Pick cohort-rolling, seasonal-rolling, pricing/bonus-based, or exploding-opportunity.
4. **Bonuses** (`references/bonuses.md`) — Each bonus needs a MAGIC name, a "how it relates to the issue" line, a dollar value, and ideally its own scarcity. Total bonus value should *eclipse* the core offer value. Tools/checklists beat additional trainings.

### Step 4 — Name the offer with MAGIC

Read `references/magic-naming.md`. Apply the MAGIC formula (Make a magnetic reason why, Announce avatar, Give a goal, Indicate timeframe, Complete with a container word). Generate 3-5 candidate names — don't just pick one. Use 3-5 of the 5 MAGIC components per name (not all are mandatory). Prefer alliteration or rhyme when it doesn't feel forced.

### Step 5 — Produce the HTML artifact

Use `assets/offer-artifact-template.html` as the template. Fill in every section. Save the final file to the outputs directory with a name like `<offer-name-slug>.html`. The artifact is self-contained (single HTML file, no external dependencies except Google Fonts) and every section is editable in-place so the user can tweak. Provide a `computer://` link to it.

## How to use the references

The references are organized by topic. SKILL.md (this file) gives the workflow; the references give the depth. Read a reference file when:

- You're entering the corresponding step of the workflow and need exact language, formulas, or examples
- The user asks a focused question about that topic ("what guarantee should I use?")
- You're in critique mode and need the checklist for that dimension

| File | When to read |
|---|---|
| `references/value-equation.md` | Always read on first invocation — it's the master mental model and you'll reference it constantly |
| `references/value-stack-process.md` | When running Step 2 of the workflow |
| `references/pricing.md` | When the user asks about price, says their price out loud, or pushes back on going premium |
| `references/guarantees.md` | When picking a guarantee, or when critiquing an offer with a weak guarantee |
| `references/scarcity-urgency.md` | When applying scarcity/urgency or critiquing artificial-feeling versions |
| `references/bonuses.md` | When designing the bonus stack |
| `references/magic-naming.md` | When naming the offer or any of its bundles/bonuses |
| `references/market-selection.md` | If the avatar/market is in question, or to validate a market before stacking |
| `references/critique-checklist.md` | When in critique mode, as the structured rubric |

## Principles to internalize (from the book)

These are the patterns Hormozi returns to repeatedly. Reflect them in your work even when not explicitly invoked:

- **Sell in a category of one.** A Grand Slam Offer cannot be directly compared to competitors. If yours can, you haven't gone far enough.
- **Charge what it's worth.** When in doubt about price, raise it. Higher prices increase results, attract better clients, and create the margin to deliver excellence (the Virtuous Cycle).
- **The pain is the pitch.** The bigger the prospect's pain, the more they'll pay. Lean into it; don't soften it.
- **Get the bottom of the equation to zero.** Time delay and effort/sacrifice are where most marketers under-deliver. Speed and ease are infinite levers.
- **Money at a discount.** Sell $100,000 of value for $10,000. Make the perceived value so much greater than the price that the answer is obviously yes.
- **Bundle, don't discount.** Never discount the core. Add bonuses that match the prospect's specific objection.
- **Guarantees need teeth.** "We guarantee results" is meaningless. "If you don't X in Y, we will Z" is real.
- **Honest scarcity beats fake scarcity.** Tell the truth about real capacity. "We're 81% full" closes more than a fake countdown.
- **Niche down to charge up.** The same product to a more specific avatar commands 10-100x the price.
- **Solve every objection.** A single unresolved objection can kill a sale. List every problem the prospect will perceive and address each one in the stack.

## Critique-mode specifics

When the user asks you to review an offer:

1. Quote what they have. Don't paraphrase — pull the exact words from their landing page / pricing / message.
2. Score each Value Equation dimension 0 or 1, then total (per Hormozi's Meditation-vs-Xanax table format). Show the score visually in the artifact.
3. Flag specific weaknesses against `references/critique-checklist.md`. Be direct — soft critique helps no one.
4. Rewrite. Don't just list issues — produce the corrected version of each section in the artifact's right column.
5. Prioritize: if the user can only fix three things, tell them which three.

## What to avoid

- **Don't paraphrase Hormozi loosely.** The frameworks have specific shapes (4 variables in the equation, 5 steps in the stack, MAGIC has 5 letters, 4 guarantee types). Get the structure right.
- **Don't soften the recommendations.** Hormozi is direct. If their guarantee is weak, say it's weak. If their price is too low, say it's too low and tell them how much to raise it.
- **Don't skip the artifact.** The user explicitly asked for a structured artifact. The conversation is the means; the artifact is the deliverable.
- **Don't pad the bundle stack.** Quality over quantity — Hormozi cuts the stack down to high-value items only. A stack of 4 great bundles is better than a stack of 12 mediocre ones.
- **Don't fabricate dollar values.** When assigning values to bundles, use defensible reasoning (what a similar component would cost standalone, hourly rate × time saved, etc.) — note the reasoning in the artifact.
- **Don't use "MUST" or "ALWAYS" in the offer copy itself.** That's the wrong register for sales copy. Use it sparingly even in your instructions to the user.

## Producing the artifact

`assets/offer-artifact-template.html` is the template. It's a self-contained HTML file (one file, inline CSS and JS, only Google Fonts as external dependency). Every text section is editable via `contenteditable` so the user can tweak in the browser. The artifact has:

- Header with offer name, tagline, and avatar
- Dream Outcome callout
- Value Stack table (bundle name, description, value) with auto-summed total
- Pricing block with the value-stack total above and the recommended price below
- Guarantee block (formula + name)
- Scarcity + Urgency block
- Bonuses table (name, description, value, scarcity tag)
- Value Equation Score widget (4 dimensions, 0/0.5/1 each, totaled)
- (Critique mode only) Original-vs-rewrite columns + prioritized fix list

To produce the artifact:
1. Read `assets/offer-artifact-template.html` (the template) AND `assets/template-snippets.md` (which documents the exact HTML for each `{{PLACEHOLDER}}` token — especially the row formats for `{{STACK_ROWS}}`, `{{BONUS_ROWS}}`, `{{COMPARE_BLOCKS}}`, `{{FIX_ITEMS}}`)
2. Fill in each placeholder using the snippet formats from `template-snippets.md`. Don't invent your own row HTML — the CSS classes matter.
3. For craft mode, set `{{MODE}}` to `craft` and leave `{{COMPARE_BLOCKS}}` and `{{FIX_ITEMS}}` empty (those sections auto-hide).
4. For critique mode, set `{{MODE}}` to `critique` and populate both the compare blocks (3-6 of them) and the three-fix list.
5. Write the result to outputs as `<offer-slug>.html` (e.g., `booked-solid-bootcamp.html`).
6. Hand the user a `computer://` link to the file.

Don't try to invent a new template each time. The user wanted a structured artifact — structure means consistency across runs.
