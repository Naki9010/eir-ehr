# Disaster Recovery

Primary source: [docs/RECOVERY.md](../RECOVERY.md) (the authoritative,
detailed procedure — read it in full before performing a real restore).

## What is implemented

Encrypted, consistent **logical** PostgreSQL backups (`pg_dump
--format=custom` streamed directly into AES-256-GCM encryption — no
plaintext dump ever touches disk) and a rigorously fenced, isolated restore
procedure (`scripts/postgres-backup.ts`, `scripts/postgres-backup-loop.ts`,
`scripts/postgres-restore.ts`), tested both generically
(`tests/backup.test.ts`) and against real EHR records
(`tests/postgres-recovery.test.ts` — signed note, lab order, staff
assignment, audit chain, and authorized clinical operation all verified
post-restore; restored sessions confirmed unable to authenticate).

## What is explicitly NOT implemented

**Point-in-time recovery (PITR)**, automatic failover, off-host immutable
storage, monitored key custody/rotation, and any agreed RPO/RTO for a
specific deployment. "These tools implement encrypted, consistent logical
backups and an isolated restore drill. They do not implement point-in-time
recovery (PITR), automatic failover, clinical certification, or a complete
production disaster-recovery service" (docs/RECOVERY.md, verbatim).

## The restore fence (why it's this careful)

The restore procedure requires a **recovery superuser**, sets the target's
connection limit to zero during restore, rejects nonempty/template/
`postgres`/actively-connected targets, and after a successful restore
transactionally purges `sessions`/`login_transactions` (but never touches
clinical/audit rows) before the operator deliberately reopens the target.
This is designed specifically to prevent the most dangerous DR mistake:
accidentally restoring into — or booting an application against — the
wrong, live database. See [docs/RECOVERY.md](../RECOVERY.md) "Restore
Procedure" for the exact numbered steps; do not improvise a shortcut.

## Application-level recovery reconciliation

After a restore, accepted-but-not-yet-applied integration receipts must be
reviewed manually — a restore can resurrect a workforce assignment that was
revoked after the backup snapshot, so revoked-assignment reconciliation is
also a required manual step before reopening access (docs/RECOVERY.md,
docs/INTEGRATIONS.md).
