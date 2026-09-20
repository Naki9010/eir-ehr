# Integration Tests

Primary source: [docs/PERSISTENCE.md](../PERSISTENCE.md) ("Recovery And
Acceptance"), `npm run test:postgres`.

## Real PostgreSQL, not an emulator

"The database test suite uses real PostgreSQL connections, not an in-memory
SQL emulator" (docs/PERSISTENCE.md). Set `EIR_TEST_POSTGRES_URL` to a
disposable administrator database and run `npm run test:postgres`; tests
create and drop their own synthetic databases/roles and never touch a
clinical database.

## What's exercised

Concurrent editors and bookings, rollback when an audit write fails mid-
transaction, provider isolation verified through direct SQL (not just
through the application API), signed-note immutability at the database
level, session/login-transaction boundaries, restart persistence, migration
failure handling, and a full verified backup restored into an isolated
database (`postgres-recovery.test.ts`).

## Application-level recovery test

`tests/postgres-recovery.test.ts` creates records through the **real EHR
services** (not raw SQL), then verifies a restored signed note, laboratory
order, staff assignment, audit chain, and authorized clinical operation
after restore — and confirms restored sessions cannot authenticate. See
[../reliability/disaster-recovery.md](../reliability/disaster-recovery.md).

## Cross-provider workflow tests

`postgres-workflow.e2e.ts` and the integration/coordination/follow-up
`*.e2e.ts` suites additionally exercise full user workflows against a
PostgreSQL-backed instance, not only SQLite — catching provider-specific
behavior differences the unit-level contract tests alone might miss.
