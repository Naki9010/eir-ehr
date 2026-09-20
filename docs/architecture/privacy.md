# Architecture: Privacy (cross-cutting view)

This is the architectural summary; the full privacy documentation set lives
under [../privacy/](../privacy/README.md).

## Where privacy lives in the architecture

- **Visibility filtering** is centralized in `packages/visibility.ts`, which
  determines what a clinician vs. patient/proxy view of the chart, history,
  change feed, and FHIR export may contain (drafts, proposals, internal
  tasks/appointments, coordination records, and monitoring/follow-up
  oversight records are clinician-only).
- **Logging** deliberately excludes request bodies, authorization headers,
  identifiers, and clinical record content at the server layer (see
  [docs/OPERATIONS.md](../OPERATIONS.md)).
- **Browser storage** never holds records or bearer tokens
  (`localStorage`/`sessionStorage`/IndexedDB) — see
  [../architecture/frontend.md](frontend.md).
- **AI evidence** is bounded to the authorized encounter context, not the
  full chart or cross-patient data (see [../ai/README.md](../ai/README.md)).
- **Third-party data flows** (lab transport, notification gateway,
  coordination notifications, external risk-engine HTTP) send narrowly
  scoped payloads by explicit protocol design — e.g. the follow-up
  notification payload contains no patient name, ID, or clinical text at
  all (see [docs/FOLLOW-UP.md](../FOLLOW-UP.md)).

See [../privacy/data-flows.md](../privacy/data-flows.md) for the full,
system-by-system data-flow inventory.
