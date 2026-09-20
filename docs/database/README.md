# Database Documentation

Primary source: [docs/PERSISTENCE.md](../PERSISTENCE.md),
`plugins/storage-sqlite.ts`, `plugins/storage-postgres.ts`,
`packages/postgres-migrations.ts`, `packages/contracts.ts` (`Store`
interface). See also [.claude/rules/database.md](../../.claude/rules/database.md).

## Contents

- [schema.md](schema.md) — actual tables (SQLite baseline; PostgreSQL is equivalent + RLS)
- [entities.md](entities.md) — the generic entity model and the `kind` values in use
- [relationships.md](relationships.md) — how entities reference each other
- [migrations.md](migrations.md) — the SQLite `user_version` gate and PostgreSQL's operator-run migration ledger
- [indexes.md](indexes.md) — indexes that exist today
- [transactions.md](transactions.md) — the unit-of-work contract
- [concurrency.md](concurrency.md) — optimistic locking and PostgreSQL serializable isolation

## Two providers, one contract, no implicit migration between them

Both providers implement the same `Store` interface. Switching providers is
a profile edit; **there is no automatic data migration from SQLite to
PostgreSQL** — moving real clinical data requires an explicitly reviewed
migration with patient/reference reconciliation (docs/PERSISTENCE.md).
