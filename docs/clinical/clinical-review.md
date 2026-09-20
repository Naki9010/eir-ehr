# Clinical Review (Audit Review Surface)

Primary source: [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md)
("Review And Extension Boundaries").

## What clinical reviewers see

Reviewers with `audit.review` see unit-scoped audit events — never patient
charts directly through this surface. Filters: staff ID, patient ID, and
outcome, with cursor pagination (avoiding a last-200-events blind spot on a
busy unit). The UI supports older-page navigation, page export, and
append-only assessments (`justified` / `follow-up`) tied to a specific event
sequence number and hash.

## Review integrity rules

- Reviewers cannot self-review their own events.
- Earlier assessments remain visible even after a new one is added — review
  history is itself append-only.
- Audit entries capture actor, assignment, unit, purpose, outcome, and
  authentication method.
- The legacy broad `/audit` route is blocked under clinic policy; permission
  denials and authenticated `403`s are themselves logged.

## Explicit limitation

Hash-chain verification is **not** external anchoring — see
[../architecture/audit.md](../architecture/audit.md). There is no automated
suspicious-access detection, scheduled review assignment, case ownership/
escalation workflow, or patient-facing access report. Manual review
functionality existing is not evidence that reviews are actually staffed or
performed in a given deployment (docs/REGULATORY-ALIGNMENT.md).
