# ADR-0004 — Enforce Signed-Note Immutability At The Database Layer

## Status
Accepted

## Date
2026-09-18 (initial clinical record model, per [docs/ARCHITECTURE.md](../ARCHITECTURE.md))

## Context

A signed clinical note must never be alterable after signing — this is a
hard patient-safety and legal requirement (CLAUDE.md §8, §12). Relying
solely on application-layer checks (e.g. "the API handler checks
`status === 'signed'` before allowing an update") is fragile: a future bug,
a different code path, or a direct-store call from a new feature could
bypass it.

## Decision

Enforce signed-note immutability with a **database trigger**, not only
application logic — in `plugins/storage-sqlite.ts`'s schema (and its
PostgreSQL equivalent). Any attempt to update a signed note's underlying
row is rejected by the database itself, regardless of which code path
attempted it.

## Alternatives Considered

- Application-layer-only enforcement — rejected as insufficiently robust
  for a hard safety invariant; a single missed check anywhere in current or
  future code could violate it silently.
- A separate "signed notes" table copied out of the mutable table on
  signing — rejected as adding complexity and a second source of truth for
  little additional protection over a trigger-enforced immutability
  constraint on the same table.

## Why

Defense in depth for the single most safety-critical invariant in the
clinical record model: even a bug in a brand-new feature cannot silently
alter a signed note, because the database itself refuses the write.

## Consequences

**Positive**: this guarantee holds regardless of future application code
changes, including ones that forget to check note status.

**Negative**: any legitimate future need to alter signed-note storage
(e.g. a schema migration touching that column) must explicitly account for
the trigger, not just write a normal `UPDATE`.

## Security Impact

Reduces the blast radius of an application-layer authorization bug for
this specific, highest-risk-tier action (CLAUDE.md §60 rates clinical
notes HIGH risk).

## Privacy Impact

None directly.

## Clinical Impact

This is the mechanism behind [../clinical/clinical-notes.md](../clinical/clinical-notes.md)'s
immutability guarantee — amendments are always new records referencing the
original, never in-place edits.

## Interoperability Impact

FHIR `DocumentReference` export reflects this same immutability — an
exported signed note's content cannot drift from what was actually signed.

## Reliability Impact

None beyond the trigger's own reliability, which is part of the database
engine itself.
