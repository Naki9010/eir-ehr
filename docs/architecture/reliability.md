# Architecture: Reliability (cross-cutting view)

This is the architectural summary; the full reliability documentation set
lives under [../reliability/](../reliability/README.md).

## Where reliability lives in the architecture

- **Transactions**: a domain mutation, its version snapshot, and its audit
  write commit atomically or not at all (see [../database/transactions.md](../database/transactions.md)).
- **Queues**: lab result intake, outbound lab orders, follow-up
  notifications, and coordination notifications all use durable, leased,
  retryable rows in the same store rather than an external message broker —
  see [../reliability/queues.md](../reliability/queues.md).
- **Idempotency**: outbound messages carry a stable message ID and payload
  hash (`Idempotency-Key`, `X-Eir-Payload-Sha256`); inbound duplicate
  submissions return the original receipt rather than reprocessing — see
  [../reliability/idempotency.md](../reliability/idempotency.md).
- **Workers**: background processing (`apps/integration-worker.ts`,
  `apps/follow-up-worker.ts`, `apps/deterioration-worker.ts`,
  `apps/coordination-worker.ts`) runs as separate, independently supervised
  processes — an embedded `worker: true` option exists for development only.
  A scale-to-zero, request-billed web service is explicitly documented as
  *not* a reliable background worker (docs/FOLLOW-UP.md,
  docs/INTEGRATIONS.md, docs/DETERIORATION.md).
- **Recovery**: encrypted logical PostgreSQL backup/restore is implemented
  and tested against real EHR records (`tests/postgres-recovery.test.ts`);
  point-in-time recovery and off-host immutable storage are not — see
  [../reliability/disaster-recovery.md](../reliability/disaster-recovery.md)
  and [docs/RECOVERY.md](../RECOVERY.md).

## The recurring pattern across every async workflow

At-least-once delivery is assumed everywhere external transport is involved;
the receiver is responsible for durable deduplication before acknowledging.
Eir enforces "one clinical application per connector/message identity" — it
never claims exactly-once delivery across a network boundary (docs/
INTEGRATIONS.md). This pattern repeats in follow-up notifications and
coordination notifications.
