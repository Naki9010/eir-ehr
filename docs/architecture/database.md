# Architecture: Database

Primary source: [docs/PERSISTENCE.md](../PERSISTENCE.md). Full documentation:
[../database/README.md](../database/README.md).

## Two interchangeable providers, one contract

`plugins/storage-sqlite.ts` (using Node's built-in `node:sqlite`
`DatabaseSync`) and `plugins/storage-postgres.ts` (using `pg`) both implement
the `Store` interface in `packages/contracts.ts`: `transaction`, `get`,
`list`, `searchEntities` (optional API-2 capability), `insert`, `revise`,
`audit`, `history`, `verifyAudit`, `grant`/`getGrant`, `restrict`/
`isBlocked`, session and login-transaction methods, and cursor-based
`changes`/`auditPage`. No domain plugin issues SQL directly.

- **SQLite** is single-process, used for local development, the public demo
  (one in-memory database per visitor), and the legacy default profile. WAL
  mode, full synchronous writes, foreign keys on, a busy timeout, and an
  append-only audit/version schema (`PRAGMA user_version` gated migrations).
- **PostgreSQL** is the persistent, multi-process-capable provider: one
  checked-out client per unit of work, serializable isolation, and
  provider-bound row-level security (RLS) so one runtime role can only see
  its own tenant's rows — see [../security/tenant-isolation.md](../security/tenant-isolation.md).

Switching providers is a profile edit; there is **no** implicit SQLite → 
PostgreSQL data migration.

## Runtime API v2 is a breaking contract change

Every storage and stateful service method returns a Promise. Version-1
plugins fail startup. See [../database/concurrency.md](../database/concurrency.md).

## Migrations

PostgreSQL migrations run as a separate, non-application-credentialed
operator command (`npm run db:migrate`), never automatically. See
[../database/migrations.md](../database/migrations.md) and
[docs/PERSISTENCE.md](../PERSISTENCE.md).
