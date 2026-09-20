# Clinical Safety Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md) §7–§12, §23, §57, §62. This file gives the clinical-safety agent the detailed shape.

## The record model

Every entity has: tenant, patient ID, kind, revision (version number), timestamps, and a validated payload. The API accepts clinical *commands* (`save`, `sign`, `amend`, `correct`, `close`, `complete`, …), never arbitrary JSON resource writes. Reference checks require an open encounter belonging to the same patient; each patient has at most one open encounter in this release. The full command plus its version/audit inserts commit or roll back together as one transaction.

## Note lifecycle

```
Create draft → Save (repeatable, expected-version checked) → Sign (immutable via DB trigger)
                                                                   │
                                                                   ▼
                                                    Amend = new draft, references original + reason
```

Only the draft's author can edit/sign it. A signed note cannot be revised — the database itself rejects the update, not just application logic. An amendment never mutates the original; it is a new record with a reference and a reason. A finished (closed) encounter rejects new ordinary notes but still allows amendments.

## Corrections (observations, diagnoses, allergies)

A correction retains the previous version, now marked `entered-in-error`, and creates a new record with the corrected value. There is no in-place edit of a finalized clinical fact anywhere in this model. Corrections require a reason.

## The "received ≠ reviewed ≠ acted on" distinction — apply this everywhere

| Domain | Distinct states that must never collapse into one boolean |
|---|---|
| Lab results | envelope received (durable, `202`) → matched → applied to chart → assigned for review → reviewed → follow-up action complete |
| Deterioration alerts | evaluated → alert raised → acknowledged → reassessed → resolved (only via the dedicated respond endpoint; generic task completion cannot close it) |
| Follow-up tasks | open → (contact attempt / action) → disposition (`action-required` requires `actionDueAt`; missing disposition fails safe to **open**, never silently closed) → completed (only when the actual required action happened) |
| Notifications | created → delivery attempted → delivered → user viewed/acknowledged → (separately) clinical action completed |

A background job succeeding, an HTTP 202, or a notification send succeeding is never sufficient evidence that the underlying clinical work is done.

## No deletion, no silent merge

There is no delete endpoint anywhere in the API. Erasure, identity merge/unmerge, legal retention/hold, and data migration are unbuilt, designed-on-purpose-later workflows — never bypass the audit trail to fake one of these with a direct store call. Patient merge/split does not exist in this codebase; if requested, treat it as CRITICAL-risk new work (CLAUDE.md §60) needing its own design, not a quick fix for a "duplicate patient" complaint.

## Wrong-patient safety

Test explicitly: switching patients mid-session, opening multiple patients in different tabs, stale/cached patient pages, browser back/forward, copied URLs, concurrent tabs, delayed/stale API responses arriving after a patient switch. The backend must independently authorize every request by the patient ID it names — it must never infer "still the same patient as the last request" from session state alone.

## The deterioration module's specific safety rules

The bundled `risk-vitals.ts` engine is explicitly "development fixtures, not NEWS2 or CHARTwatch" — never let output, UI copy, or documentation imply clinical validation. An alert never auto-resolves from a lower subsequent value, a corrected observation, missing data, or a model outage — only an explicit `resolve` action against the current assessment ID closes it. `no-trigger` means "this engine's configured rules didn't fire on available input," never "patient is low-risk."
