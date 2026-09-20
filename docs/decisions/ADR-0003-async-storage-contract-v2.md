# ADR-0003 — Asynchronous Storage/Service Contract (Runtime API v2)

## Status
Accepted

## Date
2026-09-19 (per [docs/PERSISTENCE.md](../PERSISTENCE.md))

## Context

Adding a real, multi-process-capable PostgreSQL provider required
`Store.transaction` and every stateful service method to support real
async I/O and PostgreSQL's client/transaction model. The original
synchronous, SQLite-only contract could not express this without either a
parallel incompatible API or a breaking version bump.

## Decision

Bump the plugin runtime API to version 2: every storage and stateful
clinical/identity/access service method returns a Promise, and version-1
plugins fail startup rather than silently running with the old (now
incorrect) assumptions. Add the optional `Store.searchEntities` API-2
capability for bounded, database-side filtering needed by queue-based
features (lab integration, follow-up).

## Alternatives Considered

- Keep the synchronous contract and make PostgreSQL fit it via blocking
  calls or a hidden sync-over-async shim — rejected as unsafe: it would
  hide real I/O latency and error modes behind a false synchronous
  interface, and would not honestly express PostgreSQL's transaction
  model (retryable serialization failures, real network I/O).
- Silent dual-support of both v1 and v2 plugins — rejected because a mixed
  runtime would make it easy to accidentally run an old plugin against
  behavior it wasn't written for (e.g. a synchronous predicate silently
  skipping an awaited authorization check — `filter(async ...)` is never a
  valid access check).

## Why

An explicit, enforced breaking version bump is safer than a silent
behavioral change for a healthcare application, where a subtly-wrong
authorization check could be a patient-safety incident, not just a bug.

## Consequences

**Positive**: PostgreSQL support integrates cleanly; the async contract
now honestly represents real transaction/retry semantics for every
provider.

**Negative**: every existing plugin needed updating (a real one-time
migration cost); any future third-party plugin must be written against v2
and re-run its full contract-test suite before being trusted.

## Security Impact

None directly, though the same release also introduced PostgreSQL RLS —
see [ADR-0006](ADR-0006-postgres-rls-tenant-isolation.md).

## Privacy Impact

None directly.

## Clinical Impact

Transaction callbacks must remain database-only and replayable (PostgreSQL
may retry the whole callback on serialization failure) — this constrains
where AI inference, notification sends, and other external effects can
live in the code, keeping them outside the atomic clinical write (see
[../database/transactions.md](../database/transactions.md)).

## Interoperability Impact

None — HTTP payloads and the chart-renderer contract are explicitly
unchanged by this version bump (docs/PLUGINS.md).

## Reliability Impact

Enables real leased-worker, retryable-transaction patterns used throughout
the lab-integration, follow-up, and deterioration queues (see
[../reliability/queues.md](../reliability/queues.md)).
