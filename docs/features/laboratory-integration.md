# Laboratory Integration

Primary source: [docs/INTEGRATIONS.md](../INTEGRATIONS.md).

## Purpose

Reliable, durable dispatch of lab orders and receipt of lab results via a
connected laboratory, with exact matching, correction handling, and
operator visibility into failures — without ever guessing a match.

## User Workflow

Clinician selects an authorized lab connector on a new order → order + task
+ envelope commit atomically → worker dispatches with retry → lab returns
results to a dedicated inbound endpoint → results are matched, applied, and
routed to review (see [follow-up.md](follow-up.md) and
[../clinical/laboratory-results.md](../clinical/laboratory-results.md)).

## Architecture

`plugins/integrations.ts` (queues, matching, lease/retry, audited
operations), `plugins/lab-transport-http.ts` (real HTTPS transport),
`packages/integrations.ts` (wire schemas), `packages/lab-application.ts`
(shared report/task transitions), `apps/web/integration-workspace.js`
(operator UI), `apps/integration-worker.ts` (standalone worker). See
[../interoperability/integrations/README.md](../interoperability/integrations/README.md).

## Data Model

Durable outbox/inbox rows with uniqueness on queue identity; payload, hash,
scope, and matching fields are immutable once written. See
[../database/schema.md](../database/schema.md) and
[../database/migrations.md](../database/migrations.md) (migration v2 adds
these constraints).

## API

`POST /api/patients/:id/lab-orders` (`connectorId`), `GET
/api/lab-connectors`, `GET /api/lab-orders/:id/delivery`,
`POST /integrations/:connectorId/results` (outside `/api`, machine
credential). Full wire contract: [docs/INTEGRATIONS.md](../INTEGRATIONS.md)
"Wire Contract".

## Authorization

`lab.order`/`lab.receive`/`lab.review` for clinical actions;
`integration.manage` (separate administrative permission) for operator
actions — pause/enable/retry only, never edit envelopes or force
application. Machine credentials are scoped per connector — see
[../interoperability/integrations/authentication.md](../interoperability/integrations/authentication.md).

## Security

HTTPS with certificate verification required for remote endpoints;
credentials are independently generated ≥32-byte tokens per direction. See
[../interoperability/integrations/authentication.md](../interoperability/integrations/authentication.md).

## Privacy

Order dispatch sends only a minimal patient identification snapshot, never
the full chart or AI context.

## Clinical Safety

Unsafe or semantically unmatched messages are durably quarantined, never
guessed into a chart; corrections require exact predecessor identification.
See [../clinical/laboratory-results.md](../clinical/laboratory-results.md).

## Audit

Every operator action (pause, retry) requires a reason and is audited;
machine writes are attributed to `connector:<id>` with audit role
`integration`.

## Interoperability

`eir.lab.v1` — Eir's own JSON protocol, explicitly not HL7 and not an
Inera contract. See [../interoperability/hl7/README.md](../interoperability/hl7/README.md).

## Error Handling

`422` malformed envelope (no receipt created); `409` changed-content replay
of a message ID; quarantine for semantically unmatched envelopes.

## Concurrency

Leased worker claims with fencing; PostgreSQL uniqueness/version guards
prevent duplicate application across replicas.

## Testing

`tests/integrations.test.ts`, `tests/integration-storage.test.ts`,
`tests/integrations.e2e.ts`; local sandbox via `npm run demo:integrations`.

## Known Limitations

No contracted Swedish laboratory connection; no coded test/specimen
catalogue; connected-order cancellation is blocked pending a partner-
specific protocol.

## Future Improvements

Tracked in [docs/INTEGRATIONS.md](../INTEGRATIONS.md) "next external
milestone": a contracted Swedish lab test endpoint, coded catalogue,
identity mapping, confirmed cancellation.
