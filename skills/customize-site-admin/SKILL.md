---
name: customize-site-admin
description: Build, check, turn on, or turn off a custom admin panel (Site Admin) at /admin for a Boomlink website, protected so only organization owners and admins can open it. Use when the user wants a custom dashboard or back office for their site, or wants to inspect, replace, or switch off the current one. Never publishes.
---

# Customize Site Admin

A Site Admin is a private dashboard at `/admin` on the user's site, visible only to owners and admins of their Boomlink organization. Every site starts with a plugin admin; a custom Site Admin replaces it once turned on.

## Guardrails

- Never publish from this workflow. Turning on a Site Admin does not publish the site.
- Ask before `site-admin-activate` or `site-admin-disable`; they change what owners and admins see at `/admin`.
- Always pass `expectedRevision` and `expectedSource` exactly as `get-site-admin` reported. Never guess them. On a conflict, inspect again.
- Do not put credentials or secrets in source. Admin pages run with restricted networking.

## 1. Inspect the current state

Call `get-site-admin` with `siteId`. It returns:

- `state` (`not-configured`, `prepared`, `active`, `disabled`) and `revision`.
- `effectiveMode`: `plugin`, `custom`, or `none`.
- `readiness`, for example `building`, `missing-descriptor`, `invalid-descriptor`, or `ready`.
- `inspectedSource` (with its exact `buildId`), `nav`, `dashboardUrl`, and `nextActions`.

Explain the current state to the user in plain words before changing anything.

## 2. Prepare a draft session

1. Pick or create the draft session for the admin work with `author-list-sessions` or `author-create-session`.
2. Call `site-admin-prepare` with `siteId`, the session's ID as `authoringSessionId`, and `expectedRevision` from step 1 (`0` the first time). This installs the `/admin` protection on that session. Preparing a new session while a Site Admin is active keeps the active one live until the new one is turned on.

## 3. Build the admin source

In the prepared session:

1. Call `author-describe-repo` and `author-list-scaffolds`; use `author-describe-scaffold-feature` for any admin-related scaffold the template offers. Guidance depends on the template version.
2. The source has three parts:
   - `boomlink-admin.json` at the source root: `{ "nav": [{ "id": "orders", "label": "Orders", "path": "/admin/orders" }] }`. At most 50 items, unique lowercase `id`s, plain-text labels, and canonical `/admin` paths (no `..`, `//`, `%`, `?`, or `#`). The file must be under 64 KiB.
   - Pages under `/admin`.
   - Server handlers under `/admin/api/*`. They rely on the verified platform identity, and browser requests that change data must send the current `x-boomlink-admin-csrf` header.
3. Write files with `author-write-files` or `author-edit-files` (hashes and anchors from the latest read), then check the build with `author-check-health`.

## 4. Check it is ready

1. Call `get-site-admin` with `siteId`, `source: "authoring-session"`, and `authoringSessionId`.
2. Continue only when `readiness` is `ready`. Fix `missing-descriptor` or `invalid-descriptor` in `boomlink-admin.json`; wait and inspect again while `building`.
3. Give the user `dashboardUrl` to try the panel. Ordinary previews and `author-test-runtime` cannot open `/admin`, so this link is the way to review it.

## 5. Turn it on

After the user confirms, call `site-admin-activate` with `siteId`, `expectedRevision`, and `expectedSource` copied from the inspection: `{ "kind": "authoring-session", "authoringSessionId": "...", "buildId": "..." }`, or for a published site `{ "kind": "published", "versionId": "...", "buildId": "..." }`.

On a published site, the live dashboard runs from the published version. Later draft edits do not change it until a new version is published with `publish-website`.

## Turning it off

Call `site-admin-disable` with `siteId` and `expectedRevision`, after confirming with the user. The site falls back to the plugin admin and the `/admin` protection stays in place.

## Rollbacks

Rolling back site code does not move the Site Admin automatically, and database data and migrations are not rolled back. After `rollback-site`, inspect with `get-site-admin` and turn the panel on again for the matching version if needed.

## Output

- The Site Admin state before and after.
- The `dashboardUrl` to review.
- Anything the user must do, such as publishing for the panel to reach the live site.
