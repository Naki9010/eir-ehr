# Reliability Documentation

Primary source: [docs/INTEGRATIONS.md](../INTEGRATIONS.md),
[docs/FOLLOW-UP.md](../FOLLOW-UP.md), [docs/RECOVERY.md](../RECOVERY.md),
[docs/OPERATIONS.md](../OPERATIONS.md). See also
[.claude/rules/reliability.md](../../.claude/rules/reliability.md).

## Contents

- [failure-modes.md](failure-modes.md) — what's assumed can fail, and how each is handled
- [retry.md](retry.md) — the shared retry pattern (backoff, jitter, attempt budget)
- [idempotency.md](idempotency.md) — message-ID/payload-hash discipline
- [queues.md](queues.md) — the leased-row queue pattern used throughout
- [recovery.md](recovery.md) — recovery behavior after a crash or restart
- [monitoring.md](monitoring.md) — what operational signals exist (and the real gap: what doesn't)
- [disaster-recovery.md](disaster-recovery.md) — encrypted PostgreSQL backup/restore

## The recurring architecture

Every asynchronous workflow in Eir (lab integration, follow-up
notification, coordination notification, deterioration evaluation) follows
the same shape: a durable database row written in the same transaction as
the triggering domain action, a leased worker that claims due work, at-
least-once delivery with idempotency keys, bounded exponential backoff, and
a visible failed/quarantined state requiring authorized intervention rather
than silent retry-forever or silent drop. Understanding this one pattern
explains most of what's in this folder.
