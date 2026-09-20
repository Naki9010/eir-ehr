# Concurrency

Primary source: [docs/PERSISTENCE.md](../PERSISTENCE.md), CLAUDE.md §31.

## Optimistic concurrency (both providers)

Every mutating clinical action requires the currently-read `version`. A
mismatch — someone else already revised the record — returns `409` rather
than silently overwriting. This is the mechanism, not merely a convention:
`Store.revise(actor, entity, version, data, action)` validates the expected
version against the stored one before writing.

## PostgreSQL: serializable isolation

One checked-out client per unit of work, serializable transaction
isolation. Contention that can't be resolved becomes an explicit conflict
(a retried serialization failure, or ultimately a stale-version `409`) —
never an automatic last-write-wins update.

## What is explicitly tested

`tests/postgres-clinical.test.ts` and related suites exercise concurrent
editors and bookings, and rollback behavior when an audit write fails mid-
transaction (see [../database/README.md](README.md) and
[../testing/integration-tests.md](../testing/integration-tests.md)).
Care-team scheduling has its own overlap-detection tests (double-booking a
clinician or patient) — see [docs/CARE-TEAM.md](../CARE-TEAM.md)
"Verification And Limits".

## The specific "never" this guarantees

Never overwrite a newer clinical version with stale data — this is true for
note saves, medication reconciliation, lab-order review, appointment
actions, and follow-up task actions alike; every one of these uses the same
expected-version pattern rather than a bespoke concurrency mechanism per
feature.
