---
name: optimize-page
description: Review a Boomlink page for conversion problems across seven dimensions (value proposition, headline, calls to action, hierarchy, trust, objections, friction), recommend prioritized fixes, and apply the agreed ones in a draft session. Use when a page gets traffic but not enough signups, sales, or contacts, or the user asks for a CRO review. Never publishes.
---

# Optimize Page

Observe before you prescribe, prioritize before you change, and measure before you declare victory. Fixing a page that converts at 2% so it converts at 4% halves the cost of every visitor.

## Guardrails

- Never publish from this workflow.
- Recommend first; apply only the changes the user agrees to.
- Change one high-impact thing at a time when the user wants to measure results.

## 1. Define success

Ask:

- What is the primary conversion (sign up, purchase, contact, download)?
- What is the current conversion rate, if known?
- Where does traffic come from (paid, organic, referral, email)?
- Any section the user already suspects?

## 2. Look at the page

1. Select a session with `author-list-sessions` (create with `author-create-session` only when none fits).
2. Find and read the route and its components with `author-find-files` and one `author-read-files` call.
3. Show the rendered page with `author-view-site`, or `preview-site` for the current site state.

Interpret what you see: three calls to action above the fold means the page is unsure what it wants; testimonials at the very bottom means proof is wasted.

## 3. Analyze seven dimensions, in order

1. **Value proposition**: can a visitor tell what is offered within 5 seconds? Does the above-fold content stand alone?
2. **Headline**: specific outcome, matching the promise of the traffic source?
3. **Calls to action**: one clear primary action visible without scrolling, copy that says what happens next, placed after persuasion points.
4. **Hierarchy and scannability**: descriptive headings, short paragraphs, the important content most prominent.
5. **Trust**: specific testimonials, logos, numbers, near decision points.
6. **Objections**: FAQ, guarantees, and risk reversal for price, complexity, and risk.
7. **Friction**: only necessary form fields, no distracting navigation on landing pages, works on mobile.

## 4. Recommend

- **Quick wins**: copy fixes, moving existing proof next to calls to action, removing friction.
- **High-impact changes**: headline rewrites (2 or 3 options with rationale), call-to-action redesign, missing trust signals.
- **Test ideas**: clear A/B hypotheses with the metric to watch.

## 5. Apply agreed changes

1. Edit with `author-edit-files` using hashes and anchors from the latest read, or `author-write-files` for new components. Pass `affectedPage`.
2. Verify with `author-check-health` and `author-test-runtime` on the route.
3. Show the result with `author-view-site` and check: value is clear above the fold, calls to action are visible, proof is near decisions, no friction in the path.

## Avoid

- Changing everything at once, so nothing can be measured.
- Adding words to fix conversion when removing distractions would work better.
- Beauty over clarity, copying competitors without their context, ignoring mobile.

## Output

- Findings per dimension, most important first.
- What was changed and what is only recommended.
- The preview, and the metric the user should watch after publishing with `publish-website`.
