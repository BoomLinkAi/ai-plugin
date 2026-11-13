---
name: getting-started
description: Orient a user in their connected Boomlink account, explain how Boomlink works, and recommend the right next website workflow. Use when a user is new to Boomlink, asks what they can do, wants an overview of their websites, or is unsure whether to create, edit, or publish a site. Also the reference for how Boomlink tools fit together.
---

# Getting Started with Boomlink

Act as a website-building coach. Read the account, interpret what it says about the user's situation, and recommend one next workflow. Do not just relay data.

This skill is read only. It recommends; the chosen workflow makes changes after the user agrees.

## How Boomlink works

Boomlink sites are real React (TanStack Start) projects. You change them in draft **authoring sessions**: isolated workspaces with a live preview. Nothing a visitor sees changes until a release is published.

```text
create-site (org connection) -> author-list-sessions / author-create-session
  -> author-describe-repo -> read, edit, verify -> publish-site (after user approval)
```

### Organization and site connections

- **Organization connection**: you can list, create, update, and delete sites (`list-sites`, `get-site`, `create-site`, `update-site`, `delete-site`, `list-site-templates`, `list-backlinks`).
- **Site connection**: the connection is bound to one site. Those organization tools are not available; use the bound site ID for every call and never try to reach another site.

Every other tool (authoring, assets, config, publishing, Site Admin) works in both.

### Sessions

Reuse sessions. Call `author-list-sessions` and pick the one that matches the work (same label or variant). Create one with `author-create-session` only when none fits. Every authoring tool takes `siteId`, and session work also takes `sessionId`.

### Plugin HTTP APIs and plugin actions are different

- **Plugin HTTP API** (`author-describe-plugins-api`): an OpenAPI document for endpoints that site *code* calls at `/api/v1/sites/{siteSlug}/_plugins/{pluginId}`, such as contact forms, newsletter signup, or site auth. Use it when writing code.
- **Plugin actions** (`plugin-list`, then `plugin-execute`): actions *you* perform on the site's plugin data, such as drafting a blog post and submitting it for approval. These write real data; they are not previews. Which actions exist depends on the site.

Plugin enablement is managed in the Boomlink app, not through these tools.

## Safety rules for every workflow

- Get the user's explicit OK before `publish-site`, `rollback-site`, `unpublish-site`, `delete-site`, `author-delete-session`, or `plugin-execute`.
- Only describe sites, templates, and results that Boomlink actually returns. A successful call can still contain an error in its payload; report failures as failures.
- Never put secrets or credentials in chat or in site code. For secret config values, call `set-config` with `isSecret: true` and no value so the user enters it in the secure widget (ChatGPT) or the Boomlink app (other clients), never in chat.
- Only use an asset `url` in site code when the asset is `public`. Never use `previewUrl` in site code.

## Guardrails for orientation

- Do not create sites, sessions, releases, or assets during orientation.
- If a tool fails or the connection is not authorized, say so and stop. Do not guess what the account contains.

## 1. Understand the situation

1. On an organization connection, call `list-sites` once. Note each site's name, ID, and status (draft or published). On a site connection, call `list-versions` for the bound site instead to see whether it has been published.
2. Call `list-site-templates` only if the user has no sites or mentions starting something new (organization connection only).
3. Reuse these results for the rest of the conversation instead of calling the same tools again.

## 2. Interpret

| What you see | What it suggests |
| --- | --- |
| No sites | The user should start with a new site |
| Only drafts | Work is in progress; editing or publishing is likely next |
| Published sites and a stated change | An edit to an existing site, published only when approved |
| The user names a goal ("launch my studio page") | Match the goal to a workflow below |

## 3. Recommend one workflow

Recommend exactly one, with a short reason tied to what you saw:

- `create-website`: build a new draft from a template.
- `edit-website`: change content, pages, or navigation of an existing site, without publishing.
- `frontend-design`: lift the visual design of existing pages.
- `write-copy`: rewrite page copy so it converts.
- `optimize-page`: review a page for conversion problems and fix the biggest ones.
- `seo-setup`: titles, descriptions, schema markup, and AI search visibility.
- `customize-site-admin`: build or change the site's custom admin dashboard.
- `publish-website`: take approved changes live.

Ask the user to confirm before starting it.

## Output

- A one-paragraph summary of the account (how many sites, which are drafts or published).
- The recommended workflow and why.
- One question asking whether to proceed.
