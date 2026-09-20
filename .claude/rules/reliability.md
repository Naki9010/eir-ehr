# Reliability Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md) §43–§44. This file gives the reliability agent the detailed shape.

## Workers are supervised polling loops, not a message broker

`apps/integration-worker.ts`, `follow-up-worker.ts`, `deterioration-worker.ts`, `coordination-worker.ts` each require an explicit `EIR_CONFIG` (never a default), refuse to start if the target profile also has an embedded `worker: true` (preventing double-processing from two places at once), and support a bounded `--once` mode for externally scheduled invocation. No Redis/Kafka or other mandatory external queue exists — durability comes from the database (leases, retry state, cursors), not a broker.

## Patterns already in the codebase — reuse them for any new async work

- **Lease + fencing**: a worker reserves due work with an expiring lease; a new lease fences off a late/stale worker rather than allowing double-processing.
- **Bounded exponential backoff with jitter**: capped at one hour, with a bounded attempt budget; an audited manual replay resets the *budget*, not the message's immutable identity.
- **Idempotency by stable identity**: `messageId` + `payloadHash` for lab integration, `(record.id, record.version)` for the change feed — never rely on "this probably won't be redelivered."
- **Cursor-bounded batches**: the deterioration worker visits at most 50 monitors per unit per cycle, bounded to 2,000 encounter records, using a persisted cursor — an unbounded scan is a reliability bug even if it's currently fast.
- **Visible failure over silent failure**: unmatched/invalid integration envelopes are durably quarantined for operator review; a worker failure does not silently clear existing pending work.

## Non-negotiables

- Assume at-least-once delivery everywhere in networked/queued paths; correctness comes from idempotency and version checks, not a delivery promise.
- A request-billed, scale-to-zero deployment (e.g. Cloud Run) is explicitly not a substitute for a continuously running worker for anything time-sensitive — flag this if a devops change would introduce it for deterioration monitoring or lab processing.
- PostgreSQL concurrency/version checks must prevent duplicate alert/task commits across multiple worker replicas, even though replicas may still duplicate the *inference/network call itself* — dedupe at the commit, not just at the call site.
- A successful HTTP status code is never proof that a clinical workflow actually completed — trace the real state transition.

## Backup/restore interaction with in-flight work

Restoring a backup can replay outbox rows for messages that were accepted by a partner after the backup was taken — receiver-side idempotency and reconciliation are mandatory, not optional cleanup. Stop ingress/workers and fence the original deployment before restoring; review accepted-but-unapplied receipts before restart (docs/INTEGRATIONS.md "Recovery And Limitations", docs/RECOVERY.md).

## Operational visibility

Anything new and asynchronous needs a visible signal for its failure mode — a quarantine count, an oldest-pending-work age, a failed-cycle counter — matching the existing operations UI pattern, not just a log line nobody watches.
