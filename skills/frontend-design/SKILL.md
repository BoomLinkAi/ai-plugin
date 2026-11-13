---
name: frontend-design
description: Give existing Boomlink pages a distinctive, production-grade visual design that follows the site's design tokens, avoiding generic AI-looking layouts. Use when the user wants a page to look better, more polished, more on-brand, or more memorable. Works in a draft authoring session and never publishes.
---

# Frontend Design

Create distinctive, polished page designs in a draft authoring session. Intentional design beats generic design; bold maximalism and refined minimalism both work.

## Guardrails

- Never publish from this workflow.
- Follow the site's design principles and tokens. Change them only when the user asks for a new direction.
- Change only the pages and components the user asked about.

## 1. Understand the brief

Ask only what you cannot infer:

- **Purpose and audience**: what the page is for and who it speaks to.
- **Tone**: for example minimal, editorial, luxury, playful, brutalist, organic, retro-futuristic.
- **Differentiation**: the one thing a visitor should remember.

## 2. Load the session and the design system

1. Select or create a session with `author-list-sessions` (create with `author-create-session` only when none fits).
2. Call `author-describe-repo`. Read `designGuidance.designPrinciples` and `designGuidance.designTokens`; these are the site's agreed colors, typography, and spacing. `siteConfig` lists available config keys.
3. Call `author-describe-scaffold-feature` with `scaffold-shared-component` (or the name `templateGuidance` gives) before creating shared components.
4. Find routes and styles with `author-find-files`, then read them in one `author-read-files` call (for example `src/styles.css` and the target route).

## 3. Design direction

- **Typography**: pair a distinctive display font with a refined body font, loaded with Google Fonts `<link>` tags in the root layout. Avoid Inter, Roboto, Arial, and system fonts as the display face.
- **Color**: commit to a cohesive palette expressed as CSS custom properties in `src/styles.css`, mapped from the design tokens.
- **Composition**: vary section rhythm, use asymmetry, overlap, and whitespace on purpose. Do not stack identical sections with identical padding.
- **Motion**: CSS transitions and animations for a few high-impact moments.
- **Avoid**: purple gradients on white, cookie-cutter hero plus three cards, the same treatment on every section.

## 4. Build

1. Put reusable pieces in `src/components/` (for example `Hero.tsx`).
2. Prefer CSS modules for scoped styles and `src/styles.css` for site-wide theme variables.
3. Edit existing files with `author-edit-files` using hashes and anchors from the latest read; create new files with `author-write-files`. Pass `affectedPage` for the page you changed.

## 5. Verify

1. `author-check-health` for `buildStatus` and `buildErrors`.
2. `author-test-runtime` on each changed route.
3. `author-view-site` to show the preview, then ask the user to review it on desktop and mobile and iterate.

## Output

- The design direction in two or three sentences.
- What changed, by file and route.
- Check results and the preview.
- Next step: `write-copy` for the words, or `publish-website` when approved.
