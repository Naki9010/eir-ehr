# Reconciliation

Primary source: [docs/INTEGRATIONS.md](../../INTEGRATIONS.md) ("Recovery And
Limitations").

## Failure classes and how they're handled

| Failure | Handling |
| --- | --- |
| Network fault, `429`/`5xx`, missing order/predecessor acknowledgement | Automatic retry (bounded, backed off) |
| Wrong identity, conflicting correction, restriction, invalid acknowledgement, unavailable review ownership | Requires human intervention — not auto-retried |
| Clinical/storage failure mid-application | Rolled back before the retry state is recorded — never a partial apply |

## Pause and cancellation semantics

Pausing a connector stops new claims and rejects new incoming messages;
in-flight transmissions can still finish and keep their acknowledgement —
already-transmitted bytes cannot be retracted. **Connected-order
cancellation is explicitly blocked** until a partner-specific confirmed-
cancellation protocol exists, to avoid displaying a false local
cancellation while the lab still processes the order. Reconciliation with
the lab is required in that case, not a local override.

## Recovery-time reconciliation

Restoring a clinical database from backup can replay outbox rows for
messages that were already accepted after the backup point — receiver
idempotency (see [retry-and-idempotency.md](retry-and-idempotency.md)) and
manual reconciliation of accepted-but-unapplied receipts are mandatory
before reopening the restored deployment. See
[../../reliability/disaster-recovery.md](../../reliability/disaster-recovery.md)
and [docs/RECOVERY.md](../../RECOVERY.md).

## Operational ownership (not automated)

Operational owners must monitor failed cycles, oldest pending work,
quarantined results, and overdue clinical tasks, with agreed response
times and telephone fallback. The integration-operations UI supports
investigation/retry — it does not page, SMS, or provide certified critical-
result escalation on its own.
