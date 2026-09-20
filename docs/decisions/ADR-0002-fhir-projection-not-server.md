# ADR-0002 — FHIR As An Export Projection, Not A Full FHIR Server

## Status
Accepted

## Date
2026-09-18 (initial FHIR export); reaffirmed 2026-09-19 per [docs/FHIR.md](../FHIR.md)

## Context

Eir needs to expose clinical data in a widely-understood interoperability
format. Building a general FHIR REST server (resource-type read/search/
write, `$validate`, `_history`, subscriptions, `CapabilityStatement`) is a
much larger undertaking than a read-only export, and risks creating a
second, parallel source of truth if not carefully bounded.

## Decision

Keep Eir's own record model canonical. Implement FHIR support as a
**read-only export projection** (`GET /api/patients/:id/export/fhir`,
`plugins/fhir-r4.ts`) that reads the same authorized entity snapshots the
rest of the API uses and maps them to a FHIR R4 `Bundle`. No dual-write
openEHR/FHIR database; no general FHIR REST server.

## Alternatives Considered

- Adopting HAPI FHIR/Medplum as the primary data store — considered as a
  future evaluation for a full FHIR service, but rejected for now because
  it would require reconciling two representations of the same clinical
  record without an established reconciliation protocol
  (docs/ARCHITECTURE.md "Clinical System And Exchange Network").
- A FHIR-native domain model throughout the application — rejected because
  FHIR resource shapes don't map cleanly onto Eir's own clinical-command/
  versioning/audit model, and would couple the domain layer to a
  standard's evolution.

## Why

A projection at the boundary keeps FHIR export additive and low-risk: it
can be extended resource-by-resource without touching the clinical
domain's transaction, authorization, or audit guarantees.

## Consequences

**Positive**: FHIR export authorization and audit reuse the exact same
mechanisms as every other read (see
[../interoperability/fhir/resources.md](../interoperability/fhir/resources.md)).

**Negative**: no FHIR-native querying/subscription capability; a consumer
that needs true FHIR REST semantics cannot be served directly — they would
need a separate, explicitly built FHIR server layered on top (see
[docs/FHIR.md](../FHIR.md) "Next Interoperability Milestone").

## Security Impact

Export authorization and chart access are checked inside the same storage
transaction as any other authorized read.

## Privacy Impact

Visibility filtering (`packages/visibility.ts`) applies identically to
FHIR export as to any other authorized view.

## Clinical Impact

None — the projection reads current authorized state; it cannot itself
create or modify clinical facts.

## Interoperability Impact

No profile/implementation-guide conformance is asserted (see
[../interoperability/fhir/profiles.md](../interoperability/fhir/profiles.md)).
A future full-FHIR-server milestone remains possible without redesigning
this boundary.

## Reliability Impact

None beyond ordinary read-path reliability — export failure never affects
clinical write availability.
