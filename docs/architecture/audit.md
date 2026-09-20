# Architecture: Audit

Primary source: [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md)
("Review And Extension Boundaries"), [docs/OPERATIONS.md](../OPERATIONS.md).

## Storage shape

SQLite: `CREATE TABLE audit (seq INTEGER PRIMARY KEY AUTOINCREMENT, tenant
TEXT NOT NULL, body TEXT NOT NULL, previous TEXT NOT NULL, hash TEXT NOT
NULL)` (`plugins/storage-sqlite.ts`) — each row hashes its own body plus the
previous row's hash, forming an append-only chain verified by
`Store.verifyAudit()` at startup. PostgreSQL implements the equivalent chain
with a fixed-search-path `SECURITY DEFINER` append function
(`eir.append_audit`) so the runtime role cannot bypass it — see
[docs/RECOVERY.md](../RECOVERY.md) for the exact owner/policy names that must
be preserved across a restore.

## What gets written

`Store.audit(actor, action, patientId?, entityId?, outcome?)` is called
inside the same transaction as the mutation or disclosure it records —
authorization decisions are appended *before* data is returned, and a
successful write's audit entry commits atomically with the write. Reviewers
query via `auditPage`/`auditEntries`/`auditEntry`, scoped to their own unit
via `AccessReview.list`, with cursor pagination (`before`, `limit`,
`actorId`, `patientId`, `outcome`).

## Explicit limitation

Hash-chain verification proves the chain hasn't been altered *by anything
that isn't a privileged database operator* — it is **not** external
anchoring. A database administrator could rewrite and recompute the whole
chain. Immutable external storage, digest anchoring, automated suspicious-
access detection, scheduled review, and case ownership/escalation are not
implemented. See [../security/README.md](../security/README.md) and
[.claude/agents/audit.md](../../.claude/agents/audit.md).
