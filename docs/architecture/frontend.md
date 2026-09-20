# Architecture: Frontend

Primary source: [docs/PLUGINS.md](../PLUGINS.md) ("Replace The Chart"),
[docs/ARCHITECTURE.md](../ARCHITECTURE.md) ("UX Contract"). Full frontend
documentation: [../frontend/README.md](../frontend/README.md).

## Shape

`apps/web` is hand-written ES modules served statically — no frontend
framework, no bundler step in development. Files include `app.js` (shell),
`clinical-workflows.js`, `care-team.js`, `draft-editor.js`,
`access-workspace.js`, `coordination-workspace.js`,
`deterioration-workspace.js`, `follow-up-workspace.js`,
`integration-workspace.js`, `module-workspace.js`, `diagnosis-picker.js`, and
`style.css`. All screens talk to the same authenticated JSON API documented
in [../api/README.md](../api/README.md); there is no separate frontend-only
authorization layer, and none should ever be added (CLAUDE.md §34, §67).

## The chart-renderer contract

`apps/web/renderers/timeline.js` and `table.js` each export
`render(target, records)`. They receive the same authorized, immutable JSON
record snapshots from the shell — never a database handle, a bearer token,
or a signing capability. They remain trusted same-origin code, not a security
sandbox. A new renderer is added under `apps/web/renderers/<id>.js`, its ID
added to `chartRenderers` in the active profile, and optionally set as
`defaultRenderer`. IDs permit only lowercase letters, digits and hyphens
(preventing path traversal).

## UX contract (architectural invariants, not styling preference)

- Patient identity remains visible through every chart view.
- An allergy absence is displayed as unknown, never as confirmed absence.
- Notes display draft/signed state and attribution.
- Version conflicts surface as reload-required errors — the UI never
  silently overwrites a newer version with stale local edits.
- The browser stores no records or bearer tokens in `localStorage`.

## Replaceability boundary

The shell can be replaced entirely: the JSON APIs are independent of its
DOM/layout. A third-party shell must handle identity persistence securely,
display patient identity and record status, preserve optimistic concurrency,
and keep AI review separate from signing. Full independent shell packaging
and manifest installation are future distribution work; replacing the source
directory works today.
