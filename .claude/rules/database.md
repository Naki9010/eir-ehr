# Database Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md) §30–§32. This file gives the database agent the detailed shape.

## Two providers, one contract

`plugins/storage-sqlite.ts` and `plugins/storage-postgres.ts` both implement `Store` from `packages/contracts.ts`. SQLite is for local/disposable use: WAL mode, full synchronous writes, foreign-key checks, a busy timeout, parameterized queries, append-only version/audit guards, and a trigger that makes signed notes immutable at the database level. PostgreSQL is for persistent deployments: independent per-unit-of-work clients, serializable transactions, role-bound row-level security, and a separately operated migration ledger.

Runtime API v2 made every `Store` (and `Access`/`Clinical`/`Workforce`/`Identity`/`CareTeam`/`Medications`/`Laboratories`/`Fhir`/`AIReview`) method asynchronous. A synchronous array predicate that calls into one of these (`array.filter(x => access.check(...))`) is a bug — `filter` doesn't await, so it silently treats every call as a truthy promise and is never a real check. Replace with an explicit awaited loop.

## Transactions

`Store.transaction(async () => { ... })` callbacks must be **database-only and replayable**. PostgreSQL may retry the entire callback verbatim on a serialization failure or deadlock, with bounded backoff — so the callback must have no side effects that would misbehave on a second execution (no network calls, no AI inference, no notification sends, no non-deterministic IDs generated inside it unless idempotent). A stale expected-version conflict is an explicit, surfaced conflict — it is never silently retried as if it were a fresh write. Nested transaction failures poison the entire outer unit of work.

Domain code re-checks permissions and reference/version state *inside* the committing transaction, not just before it starts — state can change between the initial check and commit under concurrent load.

## Migrations

Run via `npm run db:migrate -- --tenant <id> --runtime-role <role> [--local-development-only]` using an administrator credential (`EIR_POSTGRES_MIGRATION_URL`), distinct from the runtime's own restricted database role. The runtime role deliberately has no superuser, BYPASSRLS, ownership, or schema-creation rights, and cannot edit tenant mappings, migration metadata, or existing audit/version rows — never propose widening it.

Follow expand → migrate → verify → switch → contract for anything that changes shape under live data: add the new column/table (expand), backfill (migrate), verify counts/invariants, switch application code to the new shape, only then drop the old one (contract) in a later, separate change.

## Row-level security

Every provider-data table has forced RLS keyed to the authenticated database role — not a request header, not an application-set session variable that a bug could leave stale. A new table needs its RLS policy designed alongside its schema, not bolted on afterward.

## `searchEntities` (API-2 optional capability)

Used by the integration and deterioration workers for bounded, keyset-paginated queries; both bundled stores implement it and startup fails without it for a profile that needs it. Any new bulk-scan need should use this pattern (bounded page size, cursor, database-side filter) rather than pulling an unbounded result set into application code.

## Testing

`npm run test:postgres` (or the `postgres-*.test.ts` files under `npm run check`) requires `EIR_TEST_POSTGRES_URL` pointed at a disposable administrator database — tests create their own synthetic databases/roles. Never point this at a shared, staging, or clinical database.
