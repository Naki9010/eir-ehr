# Migrations

Primary source: [docs/PERSISTENCE.md](../PERSISTENCE.md) ("Migrations").

## SQLite

Schema evolution is gated by `PRAGMA user_version` inside
`plugins/storage-sqlite.ts` itself — there is no separate migration script;
opening the database applies the schema for a fresh (version 0) database,
and a version newer than the application understands causes a hard startup
failure ("Database version newer than application") rather than a silent
downgrade attempt.

## PostgreSQL

Migrations run as a **separate, operator-only command**
(`npm run db:migrate -- --tenant YOUR_PROVIDER_ID --runtime-role
YOUR_EXISTING_RUNTIME_ROLE`), never automatically and never with the
application's own runtime credentials. DDL and the version/checksum ledger
commit together under a migration lock
(`packages/postgres-migrations.ts`). Startup itself rejects: pending
migrations, a newer migration history than the running code expects,
modified migration history (checksum mismatch), unsafe runtime permissions,
disabled RLS, and an invalid audit chain — all fail-closed, not
best-effort warnings.

The operator credential (`EIR_POSTGRES_MIGRATION_URL`) is explicitly
distinct from the application runtime credential (`EIR_DATABASE_URL`); the
runtime role cannot alter tenant mappings, migration metadata, or existing
audit/version rows even if compromised (see
[../security/tenant-isolation.md](../security/tenant-isolation.md)).

## The integration-runtime migration example

PostgreSQL migration **version 2** specifically adds queue indexes,
uniqueness constraints, and immutable-message guards for the lab
integration runtime, and must be applied with operator credentials
**before** new API/worker processes referencing it start
(docs/INTEGRATIONS.md).

## Migration pattern followed

CLAUDE.md §30's preferred pattern — Expand → Migrate → Verify → Switch →
Contract — is the intended discipline for schema changes; the checksum
ledger and startup rejection of "modified migration history" are the
concrete mechanisms that make this pattern enforceable rather than
aspirational.

## Explicit non-goal

There is no implicit SQLite → PostgreSQL data migration tool. Moving real
data between providers is a reviewed, patient/reference-reconciled
migration project, not a configuration change (docs/PERSISTENCE.md).
