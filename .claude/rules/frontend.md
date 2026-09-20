# Frontend Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md) §34–§38. This file gives the frontend/ux agents the detailed shape.

## Stack

`apps/web/` is plain, dependency-light JavaScript — no React/Vue/Angular/build-heavy SPA framework. Keep it that way; CLAUDE.md §4 requires a strong documented reason (and repository precedent) before introducing one, and neither exists today.

## Chart renderer contract

`apps/web/renderers/timeline.js` and `table.js` each export `render(target, records)`. They receive authorized, immutable JSON snapshots only — no database handle, no bearer token, no signing capability. A new renderer:

1. Lives at `apps/web/renderers/<id>.js`.
2. Is registered in `chartRenderers` (and optionally set as `defaultRenderer`) in the active `eir.*.config.json`.
3. Uses an ID of lowercase letters/digits/hyphens only — this is a path-traversal guard, not a style preference.
4. Never mutates clinical records directly — any write goes through the authorized `/api` command endpoints from a workflow plugin, never from renderer code.

## Shell modules

`apps/web/care-team.js` and `draft-editor.js` are separate from the renderers and must not bypass clinical authorization or record-version checks by calling storage directly — they call the same `/api` endpoints as everything else.

## UX contract (existing, don't regress it)

- Swedish labels throughout the clinical UI.
- Responsive, unframed sections; keyboard-accessible native controls; reduced-motion support.
- Patient identity visible through every chart view.
- An allergy absence displays as "unknown" — never as a confirmed negative.
- Notes display draft/signed state and attribution explicitly.
- A version conflict (409) surfaces as a reload-required error — never an automatic silent merge that could overwrite a newer version.
- No records or bearer tokens in `localStorage`.

## States every screen needs

Loading, empty, error, success, unsaved-changes, and finalized/read-only are each a distinct, intentional UI state — not "whatever renders by default when data is missing."

## Accessibility

Keyboard navigation for every action a mouse can reach; visible focus; sufficient contrast; no color-only status signaling (critical lab flags, unresolved alerts, and signed/draft state all need a non-color cue too); semantic native controls (`<button>`, `<select>`, `<table>`) over custom widgets unless there's a real reason.

## Testing

Playwright e2e (`tests/*.e2e.ts`) exercises real workflows at desktop and mobile sizes with an isolated in-memory database, writing screenshots to `test-results/`. Add or extend an e2e test for any new user-facing workflow rather than relying on unit tests alone to catch UI regressions.
