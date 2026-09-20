# Rollback

## Public demo

Restore the previous Cloud Run revision **and** the matching Firebase
Hosting release together (see [deployment.md](deployment.md)) — UI/API
compatibility is only guaranteed within the same release pairing.

## Database schema (PostgreSQL)

There is no automatic migration rollback tool. The documented pattern is
Expand → Migrate → Verify → Switch → Contract (CLAUDE.md §30) — a
backward-compatible expand-first migration makes rolling back the
*application* (without a corresponding down-migration) safe; a destructive
migration does not have this property and must not be applied without a
tested rollback plan. Startup itself refuses to run against pending,
newer, or modified migration history (see
[../database/migrations.md](../database/migrations.md)), which prevents
the most common rollback hazard (an old binary silently running against a
newer, incompatible schema).

## Data restoration

Restoring from an encrypted backup is the rollback mechanism for data
corruption or loss — see [../reliability/disaster-recovery.md](../reliability/disaster-recovery.md)
and [docs/RECOVERY.md](../RECOVERY.md) for the exact, carefully fenced
procedure. This is explicitly not a routine rollback tool — it is a
disaster-recovery procedure with its own risks (e.g. resurrecting a
revoked workforce assignment) that must be reconciled before reopening
access.

## Plugin/provider rollback

Reverting a plugin composition (e.g. an AI provider, a risk engine) is a
profile edit and restart — see
[../architecture/system-overview.md](../architecture/system-overview.md).
There is no plugin hot-swap; a rollback here always requires a controlled
restart, and any in-flight clinical writes during the swap are not
supported (docs/PLUGINS.md).
