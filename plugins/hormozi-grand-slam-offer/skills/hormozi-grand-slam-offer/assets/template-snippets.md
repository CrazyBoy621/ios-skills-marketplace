# Template Snippet Formats

This file documents the exact HTML to insert into each `{{PLACEHOLDER}}` in `offer-artifact-template.html`. Use these snippets verbatim — don't invent your own structure.

## Top-level placeholders (simple text)

| Placeholder | What goes here | Example |
|---|---|---|
| `{{MODE}}` | `craft` or `critique` | `craft` |
| `{{OFFER_NAME}}` | MAGIC-formatted offer name | `Free 90-Day Booked Solid Bootcamp for Coaches` |
| `{{TAGLINE}}` | One-line emotional hook | `Fill your calendar with paying clients — without cold outreach.` |
| `{{AVATAR}}` | Specific avatar description | `solo coaches earning $5-15K/mo who want to scale past $30K/mo` |
| `{{DREAM_OUTCOME}}` | Dream outcome in prospect's voice (1-2 sentences) | `Wake up Monday morning with 8 booked discovery calls and 2 new clients already signed.` |
| `{{PRICE}}` | The price | `$4,997` |
| `{{PRICE_TERMS}}` | Payment terms | `one-time · or 3 payments of $1,797` |
| `{{PRICE_RATIONALE}}` | Why this price (1-2 sentences) | `At $4,997, this is 90% off the standalone value of $52,400. Even closing one client at $5K from this work pays for the entire program.` |
| `{{STACK_TOTAL}}` | Total of value stack | `$52,400` |
| `{{BONUS_TOTAL}}` | Total of bonuses | `$8,300` |
| `{{GUARANTEE_TYPE}}` | One of: Unconditional / Conditional / Anti-Guarantee / Implied (Performance) | `Conditional · Service Guarantee` |
| `{{GUARANTEE_NAME}}` | Memorable name | `The "Calendar Full or We Work for Free" Guarantee` |
| `{{GUARANTEE_FORMULA}}` | The "If X in Y, we will Z" sentence | `If you don't book 10 qualified discovery calls in your first 30 days, we'll keep working with you 1-on-1 — free — until you do.` |
| `{{SCARCITY_TYPE}}` | Total Cap / Growth Rate Cap / Cohort Cap | `Cohort Cap` |
| `{{SCARCITY_TEXT}}` | The scarcity sentence | `We take 25 coaches per cohort, 4 times a year. After that, the doors close.` |
| `{{URGENCY_TYPE}}` | Cohort-Based / Rolling Seasonal / Pricing-Bonus / Exploding Opportunity | `Cohort-Based` |
| `{{URGENCY_TEXT}}` | The urgency sentence | `This cohort kicks off Monday, June 3. The next one isn't until September.` |
| `{{VE_DREAM}}`, `{{VE_LIKELIHOOD}}`, `{{VE_TIME}}`, `{{VE_EFFORT}}` | 0, 0.5, or 1 | `1` |
| `{{VE_DREAM_CLASS}}`, etc. | One of `1`, `half`, `0` (matches the score for color) | `1` |
| `{{VE_TOTAL}}` | Sum of the four scores | `3.5` |
| `{{VE_GRADE}}` | Grade explanation | `Grand Slam territory. Polish the time-delay angle.` |
| `{{VERDICT_CLASS}}` | `verdict-good` (≥3), `verdict-mid` (2-3), `verdict-bad` (<2) | `verdict-good` |
| `{{VERDICT_TEXT}}` | 1-2 sentence verdict | `This is a Grand Slam Offer. The avatar is specific, the guarantee has teeth, and the stack value is 10x the price. Ship it.` |

## `{{STACK_ROWS}}` — Value Stack table rows

For each bundle in the stack, insert one `<tr>` like this:

```html
<tr>
  <td>
    <div class="stack-name" contenteditable="true">The Booked-Solid Calendar System</div>
    <div class="stack-desc" contenteditable="true">A plug-and-play outreach + qualification workflow that fills your calendar with 8-12 discovery calls per week, automatically.
      <span class="stack-reasoning">Includes: 90 cold-email templates, the qualification scorecard, the calendar-routing automation.</span>
    </div>
  </td>
  <td class="stack-value" style="text-align: right" contenteditable="true">$12,400</td>
</tr>
```

Aim for 4-7 bundles. The `stack-reasoning` span is optional but recommended — it shows what's actually in the bundle and makes the value defensible.

## `{{BONUS_ROWS}}` — Bonuses table rows

For each bonus, insert one `<tr>` like this:

```html
<tr class="bonus-row">
  <td>
    <div class="stack-name" contenteditable="true">The 60-Second Cold-Email Swipe File</div>
  </td>
  <td>
    <div class="stack-desc" contenteditable="true">20 cold-email templates that have generated $2M+ in tracked sales. Plug in your variables and send.
      <span class="bonus-tag" contenteditable="true">Never sold separately</span>
    </div>
  </td>
  <td class="stack-value" style="text-align: right" contenteditable="true">$1,497</td>
</tr>
```

Aim for 3-5 bonuses. The `bonus-tag` shows the scarcity/urgency hook.

## `{{COMPARE_BLOCKS}}` — Critique mode only

For each section being critiqued (e.g., the original guarantee vs. the rewrite), insert one block like this:

```html
<div class="compare-grid">
  <div class="compare-col original">
    <h4>Original — Guarantee</h4>
    <p contenteditable="true">"30-day money-back guarantee."</p>
  </div>
  <div class="compare-col rewrite">
    <h4>Rewrite — Guarantee</h4>
    <p contenteditable="true">"If you don't book 10 qualified discovery calls in your first 30 days, we'll work with you 1-on-1 — free — until you do. We call it the Calendar Full Guarantee."</p>
  </div>
</div>
```

Include 3-6 compare blocks covering the highest-leverage fixes. In craft mode, the `{{COMPARE_BLOCKS}}` placeholder should be replaced with an empty string (the section auto-hides).

## `{{FIX_ITEMS}}` — Critique mode only

Three `<li>` items. In craft mode, leave empty.

```html
<li><strong>Raise the price.</strong> $97/mo is below market for what's actually in this program. Test $497/mo or $1,997 one-time. The stack supports it.</li>
<li><strong>Rewrite the guarantee with teeth.</strong> Use the "If X in Y, we will Z" formula. Try: "If you don't make your first sale in 60 days, we work with you 1-on-1 free until you do."</li>
<li><strong>Name 3 bonuses with MAGIC.</strong> Pull the swipe file, the templates, and the recorded coaching call out as named bonuses with dollar values.</li>
```

## Empty-placeholder convention

If a section doesn't apply (e.g., critique-only blocks in craft mode), replace the placeholder with an empty string — the wrapper section auto-hides based on `body[data-mode]`.
