---
name: seo-setup
description: Set up technical SEO, JSON-LD schema markup, and AI search visibility for a Boomlink site by editing its page source and config in a draft session. Use when the user wants better search ranking, meta titles and descriptions, Open Graph tags, schema markup, robots and crawler access, or to be cited by AI search. Never publishes.
---

# SEO Setup

Make the site both rankable (Google, Bing) and quotable (ChatGPT, Perplexity, Google AI Overviews). All changes go into the page source in a draft session.

## Guardrails

- Never publish from this workflow.
- Schema markup must describe content that is visible on the page. Never invent reviews, ratings, or facts.
- Do not stuff keywords. Write for people first.

## 1. Understand the goals

Ask:

- The primary keywords or topics.
- Whether local SEO matters (physical location or service area).
- The main content type (landing pages, articles, products, FAQs).
- Any known SEO issues.

## 2. Audit the source

1. Select a session with `author-list-sessions` (create with `author-create-session` only when none fits).
2. Call `author-describe-repo` to learn the route structure and how the scaffold sets the document head.
3. Find routes with `author-find-files` (`pattern: "src/routes/**/*.tsx"`) and existing tags with `author-search-content` (for example `og:title`, `application/ld+json`, `canonical`).
4. Read the root layout and key routes in one `author-read-files` call.
5. `list-config` shows existing site config keys, such as a canonical domain or analytics IDs.

Check every key page for:

- **Title**: unique, 50 to 60 characters, primary keyword near the start, for example `{Keyword} | {Brand}`.
- **Meta description**: unique, 150 to 160 characters, includes the keyword naturally, gives a reason to click.
- **Open Graph**: `og:title`, `og:description`, `og:image` (1200x630), `og:type`.
- **Canonical**: a self-referencing HTTPS canonical URL on every page.
- **Headings**: one H1, ordered H2 and H3.

## 3. Fix the head and add schema

Edit route head definitions with `author-edit-files` (hashes and anchors from the latest read) and pass `affectedPage`. Add JSON-LD in a `<script type="application/ld+json">` block:

- **Organization** on the homepage: `name`, `url`, `logo`, `description`, `sameAs`.
- **Article** on posts: `headline`, `image`, `datePublished`, `dateModified`, `author`, `publisher`.
- **FAQPage** where an FAQ is visible: `mainEntity` of `Question` and `acceptedAnswer`.
- **BreadcrumbList** on inner pages.

Combine several types with `@graph`.

## 4. Structure content for AI search

- A clear 40 to 60 word definition early for "what is" questions.
- Self-contained 40 to 60 word answer passages for likely questions.
- HTML tables for comparisons instead of prose.
- Statistics with their source named.
- Visible publication and last-updated dates.

Content changes are copy changes; agree them with the user first (see `write-copy`).

## 5. Crawler access

Look for `robots.txt` (for example with `author-find-files` and `pattern: "**/robots.txt"`) and any `noindex` tags. Make sure these crawlers are not blocked: GPTBot, PerplexityBot, ClaudeBot, Google-Extended, Bingbot. Change robots rules only with the user's agreement.

## 6. Verify

1. `author-check-health` and `author-test-runtime` on each changed route.
2. Read the changed files again to confirm titles, descriptions, Open Graph, canonical, and schema are present and valid JSON.
3. Show the preview with `author-view-site`.

## Output

- A checklist of what passed, what was fixed, and what still needs the user.
- Next step: `publish-website` so search engines can see the changes.
