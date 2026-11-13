---
name: create-website
description: Create a new Boomlink website or landing page draft from a short brief and a starter template, then build its pages in an authoring session. Use when the user wants a new site, portfolio, landing page, or campaign page. Needs an organization connection. Do not use for changing an existing site or for publishing.
---

# Create Website

Turn a short brief into a new draft site built from a real Boomlink template, with real pages and copy. The result is a draft with a preview, never a published site.

## Guardrails

- Create drafts only. Always pass `launchMode: "draft"` unless the user explicitly asks to create and publish in one step and confirms it.
- Choose a template from the catalog Boomlink returns. Never invent a template ID.
- Do not change site access. New sites keep Boomlink's default unless the user asks for a specific access policy.
- Do not modify or delete existing sites.
- `create-site` and `list-site-templates` need an organization connection. On a site connection, say that a new site has to be created from an organization connection or the Boomlink app.

## 1. Extract before you ask

From the user's message, infer the site name, industry, pages, audience, and tone.

- Brief has a name and at least one page or goal: state your plan in 3 to 5 bullets, then build.
- Brief is genuinely vague ("make me a website"): ask at most 3 focused questions.
- Never ask about things you can infer. "Fitness studio" already tells you the audience.

## 2. Pick a template

1. Call `list-site-templates` and show the templates, even when the brief is detailed. In ChatGPT, let the widget show the gallery.
2. Recommend one or two templates that fit and say why. Wait for the user to pick one or decline ("start blank").

## 3. Create the draft

Call `create-site` with:

- `name`: the agreed site name.
- `launchMode`: `"draft"`.
- `templateId`: the exact ID of the chosen template (omit it if the user declined).
- `idempotencyKey`: a stable key for this request, for example the slugified name plus the template ID. Reuse the same key if you retry the identical request.
- `description`, `designPrinciples`, or `access` only if the user provided them.

Report what actually happened:

- `siteId`, `status`, and `phase`.
- If `previewUrl` is present, share it. If `previewStatus` is `building`, say setup is still running (in ChatGPT, the Boomlink widget shows progress).
- If `status` is `failed`, report the error and whether it is `retryable`. Do not retry with different inputs without asking.

## 4. Get a real authoring session

`create-site` returns provisioning status, not an authoring session. Do not invent a `sessionId`.

1. Call `author-list-sessions` with the new `siteId`. Reuse a session the template created.
2. If there is none, call `author-create-session` with a descriptive `label`.
3. Call `author-describe-repo` with the session. Use `templateGuidance`, `scaffoldSummary`, and `designGuidance` (seeded design principles and tokens) as the foundation.

## 5. Build the pages in one pass

Design direction by industry (commit to one; bold and intentional beats generic):

| Industry | Display font | Body font | Palette | Tone |
| --- | --- | --- | --- | --- |
| Fitness / health | Bebas Neue, Oswald | DM Sans | Black with a vivid accent | Energetic |
| SaaS / tech | Space Grotesk, Syne | DM Sans | Blue, clean whites, grays | Clear, modern |
| Food / restaurant | Playfair Display, Cormorant | Lato | Terracotta, olive, cream | Warm |
| Creative / agency | Syne, Space Grotesk | DM Sans | Deep purple, electric blue, coral | Confident |
| Professional services | Cormorant Garamond | Source Sans 3 | Navy, charcoal, gold | Refined |
| Education / nonprofit | Nunito | Open Sans | Teal, warm yellow, soft blue | Friendly |
| Beauty / wellness | Cormorant, Playfair Display | Jost | Blush, sage, cream | Calm |

Prefer the site's seeded design tokens over this table when `designGuidance` has them.

1. Before writing a route, navigation, form, or shared component, call `author-describe-scaffold-feature` with a feature name from `templateGuidance` (for example `scaffold-page-route` or `scaffold-navigation-update`).
2. Write `src/styles.css` with CSS custom properties for the chosen colors and fonts. Add Google Fonts `<link>` tags in `src/routes/__root.tsx` when the scaffold supports it.
3. Write all route files with one `author-write-files` call and pass `affectedPage` (for example `/`).
4. Add a navigation component and include it in `src/routes/__root.tsx`, using TanStack Router `Link`.
5. For contact forms, newsletters, or other managed features, call `author-describe-plugins-api` and write the form against the documented endpoints through `src/lib/sitePluginGateway.ts` when the template ships it. Never hardcode a site slug.

Content rules:

- Write real copy from the brief. No "Lorem ipsum", "Your Company Name", or "Welcome to our business".
- Every page has at least one clear call to action.
- Mobile-friendly layouts from the start: responsive units, `clamp()`, grid and flex.
- Avoid generic gradient heroes, system fonts, and the same treatment on every section.

## 6. Verify and present

1. Call `author-check-health` and read `buildStatus` and `buildErrors`.
2. Call `author-test-runtime` with `path` set to each new route. `runtimeStatus: "ok"` only means the status was below 500; a 404 is still a problem.
3. Fix failures with targeted edits (see `edit-website`) and verify again.
4. Call `author-view-site` with the session to show the preview.

Do not publish from this workflow.

## Output

- Site name, ID, and template used.
- Provisioning status and the preview, or a clear note that setup is still running.
- Pages built and any check that still fails.
- Next step: refine with `edit-website` or `frontend-design`, or publish with `publish-website` when the user approves.
