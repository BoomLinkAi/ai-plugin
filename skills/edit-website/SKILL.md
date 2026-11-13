---
name: edit-website
description: Make focused content, page, or navigation changes to an existing Boomlink website in an authoring session, verify them, and return a preview without publishing. Use when the user wants to change copy, sections, add a page, update navigation, or fix something on a site they already have.
---

# Edit Website

Make the requested change, and only that change, in an authoring session. Read before writing, batch your calls, verify the result, and hand back a preview. Publishing is a separate workflow.

## Guardrails

- Never publish from this workflow.
- Change only what the user asked for. Do not refactor or restyle unrelated code.
- Never guess file hashes or line anchors. Use the values from the latest read.
- Report failures as failures. A successful call can still contain an error in its payload.

## 1. Select the site and session

1. Resolve the site. On an organization connection use `list-sites` or `get-site` and ask if more than one site matches. On a site connection, use the bound site.
2. Call `author-list-sessions`. Each session has `sessionId`, `label`, `parentBranchId`, and `createdAt`.
3. Reuse the session that matches the work. Ask the user if it is unclear.
4. Only if none fits, call `author-create-session` with a descriptive `label`.

## 2. Understand the project

1. Call `author-describe-repo` with the session. Use `templateGuidance` and `scaffoldSummary` as orientation, and `designGuidance` for the site's design principles and tokens.
2. For a new route, component, navigation, form, or embedded data feature, call `author-describe-scaffold-feature` with a feature name from that guidance (for example `scaffold-page-route`, `scaffold-navigation-update`, or `scaffold-shared-component`). `author-list-scaffolds` lists what exists.
3. Locate files with `author-find-files` (for example `pattern: "src/routes/**/*.tsx"`) or `author-search-content`. Do not read the whole project.

## 3. Read in one batch

Call `author-read-files` with every file you need. Each result includes a file `hash` and line anchors. If output is truncated, read the remaining range with `offset` before editing.

## 4. Edit in one batch

- Existing files: `author-edit-files` with each file's `path`, its exact `hash`, and `edits` using `from` and `to` anchors from the read. Ranges are inclusive and must not overlap. At most 20 edits per call. Replacement content is plain source, without line-number prefixes.
- New files or an intentional full replacement: `author-write-files`.
- Remove or move files with `author-delete-files` or `author-rename-files`.
- Pass `affectedPage` (for example `/` or `/pricing`) so the preview opens the changed page.
- If the response reports a stale hash or anchor, read the file again and rebuild the edit. Do not retry with the old values.
- A refused edit returns an error result with code `EDIT_FAILED` and per-file codes in `errors`. Check for it before continuing. After `APPLY_FAILED`, read the files again, because some may have changed.

### Adding a page

TanStack Start uses file-based routes: `/about` is `src/routes/about.tsx`, `/blog/$postId` is `src/routes/blog.$postId.tsx`.

```tsx
import { createFileRoute } from '@tanstack/react-router';

export const Route = createFileRoute('/about')({ component: AboutPage });

function AboutPage() {
  return <main>...</main>;
}
```

Match the styling of existing pages, give the page one clear call to action, and add a link to it in the navigation.

### Updating navigation

Read `src/routes/__root.tsx` and the navigation component together. Keep the main navigation to 5 to 7 items, most important first, with a visually distinct call-to-action link. Use TanStack Router `<Link>` and make sure it works on mobile.

## 5. Verify

1. Call `author-check-health` with the session. Check `buildStatus` and `buildErrors`.
2. Call `author-test-runtime` with `path` set to each changed route. Read `response.status` and the body. `runtimeStatus: "ok"` only means the status was below 500; a 404 or 401 is still a problem. The body and runtime logs come back in short pages: to read more, pass the returned `nextCursor` as `cursor`. Each call re-runs the route, so pages can differ between calls.
3. If the build or route fails, fix the cause with a targeted edit and verify again. Stop and explain if you cannot fix it.
4. Call `author-view-site` with the same `sessionId` to return the preview.

These checks confirm the build and server response. They are not a visual review; ask the user to look at the preview.

## Output

- What changed, by file and route.
- Build and route check results, including any remaining issue.
- The preview for the session.
- An offer to publish with `publish-website` when the user is happy.
