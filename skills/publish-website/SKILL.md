---
name: publish-website
description: Take approved changes on a Boomlink website live by preparing a release from an authoring session, confirming with the user, publishing it, and checking activation until it is done. Also covers rolling back or unpublishing. Use when the user asks to publish, launch, go live, ship reviewed changes, or revert a published site. Do not use for previews or for making edits.
---

# Publish Website

Move reviewed changes from an authoring session into a release, confirm with the user, and publish. Publishing replaces what visitors see, so every step checks the real operation state before moving on.

## Guardrails

- Always ask for explicit confirmation immediately before `publish-site`, `rollback-site`, or `unpublish-site`.
- Publish only the session or release the user chose. Ask if it is ambiguous.
- Do not change site access settings while publishing.
- Never report the site as live until activation is confirmed.
- Stop on failed, stale, busy, or conflicted operations and explain. Do not start parallel retries.

## 1. Confirm what is being published

1. Identify the site and the authoring session with the approved changes (`author-list-sessions`).
2. Ask whether this is a new launch or an update, and which pages changed.
3. Run `author-test-runtime` on the changed routes and check `response.status` and the body. Do not publish a session whose build or routes fail.
4. Check the preview with `author-view-site`: no placeholder text, links and navigation work, forms and calls to action work.

## 2. Publish-only static pages (optional)

Ask which public, visitor-independent pages should refresh only when a new release is published. Read `prerender.json` at the source root with `author-read-files`. With the user's approval, write it with `author-write-files`:

```json
{ "paths": ["/", "/pricing"], "freshness": "until-next-publish" }
```

At most 20 exact root-relative routes. No queries, wildcards, API endpoints, authenticated or personalized pages, or pages that must change between publishes. Freezing a visitor-dependent page can expose stale or private content, so leave it dynamic.

## 3. Build the release

These tools take `siteId` plus their own fields; no `sessionId` is needed.

1. `author-create-release` with a new, stable `releaseId` (for example `release-2026-11-03-homepage`). A release starts from the current main source.
2. `author-prepare-merge` with `sourceSessionId` (the approved session), `targetReleaseId`, and a stable `idempotencyKey`.
3. Read the operation with `author-get-source-operation` (`operationId`):
   - `conflicted`: show the conflicting paths. Use `author-resolve-integration` only when the right choice is clear (`source`, `target`, or `content` with the merged text). Otherwise ask the user.
   - Continue only when `status` is `ready` and `stage` is `validated`.
4. `author-apply-integration` with the `operationId` to advance the release.

## 4. Make sure the release is current

1. Call `author-list-releases`. Compare the release's `baseMainOid` with main's `headOid`.
2. If they differ, call `author-prepare-rebase` with `refKind: "release"`, `refExternalId` (the release ID), `ontoKind: "main"`, `ontoExternalId: "main"`, and a new `idempotencyKey`. Handle conflicts and apply exactly as in step 3.
3. If they match, skip the rebase.

## 5. Confirm and publish

1. Tell the user which site and release will go live, and ask for confirmation.
2. Call `publish-site` with `siteId`, `releaseId`, a stable `idempotencyKey`, and `versionBump`: `patch` for content updates, `minor` for new pages or features, `major` for breaking changes.

## 6. Confirm activation

- If the result is `pending`, call `author-get-source-operation` with `operationId` set to the returned `sourceOperationId`. Repeat until `status` is `applied`. `failed` or `stale` means it did not activate; report its `errorCode`.
- Do not call `publish-site` again while it is pending.
- Only once it is applied, report the site as live with its public URL.
- If you used `prerender.json`, compare `prerenderedPaths` in the result with the file. Missing routes are skipped; do not call a skipped route static.

## Retries

Reusing an `idempotencyKey` returns the existing operation, including a failed or stale one. After a failure, refresh the heads with `author-list-releases`, then prepare a new operation with a new key. If an operation reports the target is busy, inspect it with `author-list-source-operations` instead of retrying.

## Reverting

- **Revert published code**: call `list-versions`, let the user choose a version, confirm, then call `rollback-site` with that `versionId`. Database data and migrations are not restored.
- **Take the site offline**: `unpublish-site`, only if the user wants the site offline. It is not a code rollback.
- **Quick fix**: edit with `edit-website`, then publish a new release.
- A custom Site Admin does not follow a rollback automatically; re-inspect and re-activate it with `customize-site-admin`.

## Output

- The site, release ID, and version published.
- Activation status and the public URL once applied.
- Any step that stopped, with the reason and what the user can do next.
