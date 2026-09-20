# Patient Safety

Primary source: [.claude/rules/clinical-safety.md](../../.claude/rules/clinical-safety.md),
[docs/ARCHITECTURE.md](../ARCHITECTURE.md).

## The record model that enforces safety

Every entity has tenant, patient ID, kind, revision, timestamps, and a
validated payload. The API accepts clinical *commands* (`save`, `sign`,
`amend`, `correct`, `close`, `complete`, …), never arbitrary JSON resource
writes. Reference checks require an open encounter belonging to the same
patient; each patient has at most one open encounter in this release. The
full command plus its version/audit inserts commit or roll back together in
one transaction.

## "Received ≠ reviewed ≠ acted on" — enforced structurally, not just by convention

| Domain | Distinct states that never collapse into one boolean |
| --- | --- |
| Lab results | envelope received (`202`, durable) → matched → applied to chart → assigned for review → reviewed → follow-up action complete |
| Deterioration alerts | evaluated → alert raised → acknowledged → reassessed → resolved (only via the dedicated respond endpoint) |
| Follow-up tasks | open → contact attempt/action → disposition (`action-required` requires `actionDueAt`; a missing disposition fails safe to **open**) → completed |
| Notifications | created → delivery attempted → delivered (to gateway) → user viewed/acknowledged → clinical action completed (separate) |

A background job succeeding, an HTTP `202`, or a notification send
succeeding is never treated as evidence the underlying clinical work is
done. See [../clinical/laboratory-results.md](laboratory-results.md) and
[docs/DETERIORATION.md](../DETERIORATION.md).

## No deletion, no silent merge

There is no delete endpoint anywhere in the API. Corrections retain the
previous version marked `entered-in-error` and create a new record — there
is no in-place edit of a finalized clinical fact. Patient merge/split **does
not exist** in this codebase; if ever requested, it is CRITICAL-risk new
work (CLAUDE.md §60) requiring its own design, not a quick fix for a
duplicate-patient complaint. See [patient-identity.md](patient-identity.md).

## Wrong-patient safety

Explicitly tested scenarios: switching patients mid-session, opening
multiple patients in different tabs, stale/cached patient pages, browser
back/forward, copied URLs, concurrent tabs, delayed/stale API responses
arriving after a patient switch. The backend independently authorizes every
request by the patient ID it names — it never infers "still the same
patient as the last request" from session state alone. See
[security-test-matrix in testing/](../testing/clinical-safety-tests.md).

## The patient-safety gate (CLAUDE.md §62)

Before any clinically important feature is considered complete:

1. Is the correct patient guaranteed?
2. Is authorization enforced?
3. Is the clinical context correct?
4. Can data be lost?
5. Can stale data overwrite newer data?
6. Can duplicate events cause duplicate clinical actions?
7. Is finalized data protected?
8. Are corrections traceable?
9. Is the action audited?
10. What happens if the database fails?
11. What happens if an external system fails?
12. What happens if the request is retried?
13. What happens if two users act simultaneously?
14. Can the user misunderstand the state?
15. Can the workflow produce a wrong-patient action?

If any answer is unclear, the feature is not done.
