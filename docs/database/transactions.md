# Transactions

Primary source: [docs/PERSISTENCE.md](../PERSISTENCE.md) ("Storage Contract
v2"), [docs/PLUGINS.md](../PLUGINS.md).

## The contract

```ts
transaction<T>(fn: () => Promise<T>): Promise<T>
```

(`Store` interface, `packages/contracts.ts`.) A domain mutation, its version
snapshot, and its success/failure audit write commit together — a failed
operation never leaves a successful partial record. Nested transactions
join the same unit of work; a nested failure makes the entire unit
rollback-only.

## Hard rule: transaction callbacks are database-only

PostgreSQL may retry the **entire callback** on a serialization failure or
deadlock (bounded backoff). This means callbacks must be side-effect-free
outside the database: no network calls, model inference, notification
sends, or file writes inside `Store.transaction`. Model generation
(AI), external HTTP delivery, and similar effects happen **outside** the
transaction, with authorization and evidence rechecked before the
transaction that actually persists the result (see
[../ai/architecture.md](../ai/architecture.md) and
[../reliability/idempotency.md](../reliability/idempotency.md)).

## SQLite's transaction model

SQLite's adapter serializes callers around the single synchronous
connection, including across `await` boundaries — this is why SQLite is
documented as a single-process deployment option, not a claim of true
concurrent multi-process transactions.

## Stale versions are explicit conflicts, never silently retried as new writes

A stale expected-version write is a `409` conflict end-to-end — it is never
automatically retried as if it were a fresh write, even under PostgreSQL's
serialization-failure retry. Retrying the same *stale* version would
silently discard a concurrent editor's newer change, which is exactly what
optimistic concurrency exists to prevent (see [concurrency.md](concurrency.md)).
