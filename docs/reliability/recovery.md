# Recovery (Runtime)

This page is about process/worker recovery after a restart or crash — for
database backup/restore, see [disaster-recovery.md](disaster-recovery.md).

## Restart behavior

- **Queues**: abandoned leases become eligible for reclaim at their stored
  deadline; no manual intervention is needed for an ordinary process
  restart.
- **Sessions**: SQLite v2 and PostgreSQL both persist sessions/login
  transactions durably — a restart does not silently log everyone out,
  but a restore from backup deliberately does (see
  [disaster-recovery.md](disaster-recovery.md)).
- **Assignments/revocations/reviews**: survive restart (docs/CARE-TEAM.md,
  docs/IDENTITY-AND-ACCESS.md).
- **Worker scan cursors**: persist across restarts (follow-up, deterioration
  workers) — a restart resumes scanning roughly where it left off rather
  than restarting from the beginning of the unit's work every time.

## What restart does NOT do

Restart never re-executes an already-committed clinical transaction merely
because a caller retried — the same version/idempotency-key discipline
applies whether the retry is caused by a network blip or a process restart
in between attempts.

## Startup validation as a recovery-time safety check

On startup, both storage providers verify the audit chain
(`Store.verifyAudit()`); PostgreSQL additionally rejects pending, newer, or
modified migration history, unsafe runtime permissions, and disabled RLS —
all fail-closed rather than starting in a degraded, silently-unsafe state
(see [../database/migrations.md](../database/migrations.md)).
