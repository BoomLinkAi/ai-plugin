---
name: write-copy
description: Write or rewrite conversion-focused copy for a Boomlink page (headlines, sections, calls to action) and apply it to the page source in a draft session. Use when the user wants better wording, a stronger headline, clearer value proposition, or copy for a landing, product, about, or pricing page. Never publishes.
---

# Write Copy

Coach the user toward copy that moves visitors to act, then apply it to the page source. Most pages fail because the copy talks about the product instead of the visitor.

## Guardrails

- Never publish from this workflow.
- Change copy, not layout or styling, unless the user asks.
- Use real facts from the user. Do not invent numbers, customers, or testimonials; mark anything you need from them as a question.

## 1. Gather context

Ask only what is missing:

- **Page purpose**: what should the visitor do next (buy, sign up, contact, learn)?
- **Audience**: who is it for and what do they already know?
- **Traffic source**: ads, search, referral, or email?
- **Differentiator**: why this offer over the alternatives?
- **Tone**: formal or casual, technical or simple, bold or understated.

## 2. Read the current page

1. Select a session with `author-list-sessions` (create with `author-create-session` only when none fits).
2. Find the route with `author-find-files` (for example `pattern: "src/routes/**/*.tsx"`) or search for the current headline with `author-search-content`.
3. Read the route and its components in one `author-read-files` call.

Interpret what you find: a headline that says "Welcome" on a signup page is a disconnect; repeated sections without progression mean the copy has no narrative.

## 3. Structure

**Above the fold**: a headline with one clear outcome, a subheadline with supporting detail or proof, and the primary call to action.

**Core sections, in priority order**: social proof, the problem, benefits (not features), how it works in three steps, objection handling (FAQ, guarantees), and a final call to action.

## 4. Headlines and calls to action

Headline formulas:

- "{Achieve outcome} without {pain point}"
- "{Number} {people} use {product} to {outcome}"
- "{Key feature} for {target audience}"
- "Stop {unpleasant thing}. Start {desirable thing}."

Be specific ("Save 4 hours a week", not "Save time"). Lead with the outcome. Clarity beats wordplay.

Calls to action follow **action verb + what they get + qualifier**: "Get my free report", "Start your free trial", "See how it works". One primary call to action per section; urgency only when it is real.

Page types:

- **Landing**: match the ad or email that drove the click, one focused call to action repeated 2 or 3 times, proof near every call to action.
- **Product**: lead with the transformation, bullets for features, paragraphs for benefits, answer the top 3 objections.
- **About**: the origin story through the customer's lens, values tied to outcomes, a clear next step.
- **Pricing**: anchor on the recommended plan, name plans by audience, list what is included, add a pricing FAQ.

## 5. Apply the copy

1. Show the user the proposed copy (with 2 or 3 headline options) and get agreement.
2. Apply it with `author-edit-files`, using the `hash` and line anchors from the latest read. Pass `affectedPage`.
3. Verify with `author-check-health`, `author-test-runtime` on the route, and show the result with `author-view-site`.

## Review checklist

- Every section has one clear idea.
- Benefits lead, features support.
- Specific over vague, active voice, confident tone, no jargon the audience would not use.
- Calls to action say exactly what happens next.

## Output

- The copy that changed, by section and route.
- Any facts you still need from the user.
- The preview, and a suggestion to run `optimize-page` or `publish-website` next.
