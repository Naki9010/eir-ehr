---
name: clinical-ux-review
description: Review an Eir EHR screen or form for clinical usability and accessibility — patient-context clarity, state handling, keyboard access, color-only signaling. Use after a frontend feature is functionally correct and needs a usability pass.
---

# Clinical UX Review

See `.claude/rules/frontend.md` for the full UX contract this skill draws from.

## Fast checklist

1. Is the active patient (name/ID/DOB or equivalent) visible without scrolling in the common case?
2. Does every state — loading, empty, error, success, unsaved-changes, finalized/read-only — have an explicit, intentional treatment (not "blank until data arrives")?
3. Is critical status (unresolved deterioration alert, critical lab flag, signed vs. draft) conveyed by more than color alone?
4. Can every action reachable by mouse also be reached by keyboard, with sensible focus management after state changes (e.g. after sign, after a 409 conflict)?
5. Does a version conflict (409) surface as an explicit reload-required message, never a silent auto-merge?
6. Does an allergy with no recorded data show as "unknown," not as a confirmed negative?
7. Does a table/list with many rows (patient search, follow-up inbox, audit log) paginate or cursor rather than rendering everything?
8. Does the screen hold up at both desktop and mobile widths?
9. Is the interface honest about backend rejection — no optimistic UI update implying success before the server actually confirms it?

## Reporting

Cite the specific file and describe the concrete user-facing symptom (not just "improve accessibility") — e.g. "the follow-up action button has no visible focus ring, so keyboard users can't tell which row is selected before pressing Enter."
