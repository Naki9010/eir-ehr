# Clinical Follow-Up

Primary source: [docs/FOLLOW-UP.md](../FOLLOW-UP.md).

## Purpose

Separate "result received" from "action completed," with named ownership,
deadlines, coverage, and durable notification — so nothing outstanding is
silently lost.

## User Workflow

An order/result creates or reopens an owned task → the owner (or a covering
colleague after explicit handover) records a contact attempt or action →
explicit completion (`disposition: completed`) or explicit open
continuation (`action-required` + `actionDueAt`) → Bevakning workspace shows
open/closed work, deadlines, and delivery/worker health.

## Architecture

`eir.follow-up.policy` (`FollowUpPolicy`), `eir.notifications.http`
(`NotificationTransport`), `eir.follow-up` (`FollowUp` — transitions,
notifications, coverage, eligibility, oversight, bounded worker),
`apps/web/follow-up-workspace.js`. See
[../reliability/queues.md](../reliability/queues.md).

## Data Model

Task entities (existing clinical `task` kind) plus follow-up-specific
coverage/notification/event records, excluded from chart/history/change-
feed/FHIR paths.

## API

`GET /follow-up`, `POST /follow-up/coverage`,
`POST /follow-up/coverage/:id/cancel`, `POST /follow-up/:id/action`,
`POST /follow-up/notifications/:id/replay`. Full detail:
[docs/API.md](../API.md) "Clinical follow up".

## Authorization

`task.write` for actions and coverage scheduling. Coverage never itself
grants patient access — every handover rechecks live assignment/unit/
permission/relationship.

## Security

Notification transport requires HTTPS, a ≥32-byte bearer token, and rejects
redirects/insecure URLs.

## Privacy

Notification payload contains **no** patient name, ID, result, task ID, or
clinical text — only a fixed generic prompt and workspace URL.

## Clinical Safety

A missing disposition fails safe to **open**. Explicit completion means the
clinician affirms all necessary action is done — never inferred from
notification delivery. See [../clinical/patient-safety.md](../clinical/patient-safety.md).

## Audit

Task state, reassignment, events, and notification creation share one
transaction; delivery occurs outside transactions under fenced leases with
preserved message identity.

## Interoperability

`eir.notification.v1` — Eir-specific JSON, not FHIR or a national paging
standard.

## Error Handling

Delivery failures remain visible and require authorized, reasoned, versioned
replay — never silent retry-forever or silent drop.

## Concurrency

A correction or concurrent reassignment invalidates a stale completion
attempt (`409`).

## Testing

`tests/follow-up.test.ts`, `tests/follow-up.e2e.ts`,
`tests/integration-storage.test.ts` (bounded status filtering).

## Known Limitations

No SMS/email/pager contract; bundled policy defaults are development
fixtures, not clinical guidance; a scale-to-zero web service is explicitly
insufficient as the worker's host.

## Future Improvements

Clinically approved timing/escalation policy per deployment, staffed
fallback, independent monitoring of worker freshness — see
[docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md) "Clinical safety
of follow up".
