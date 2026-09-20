# Queues

Primary source: [docs/INTEGRATIONS.md](../INTEGRATIONS.md) ("Modules"),
[docs/FOLLOW-UP.md](../FOLLOW-UP.md), [docs/SAMVERKAN.md](../SAMVERKAN.md).

## No external message broker

Queues are durable rows in the same SQLite/PostgreSQL store as clinical
data, using the optional API-2 `Store.searchEntities` capability for
bounded, database-side filtering and keyset pagination — not Redis, not
Kafka, not a mandatory paid service. Startup fails if a selected storage
provider doesn't implement this capability, rather than silently degrading
to an unbounded scan.

## Worker processes

| Worker | Entry point | Scans |
| --- | --- | --- |
| Lab integration | `apps/integration-worker.ts` | One outgoing + one incoming message per configured connector per cycle |
| Follow-up | `apps/follow-up-worker.ts` | Up to 50 open tasks per configured unit, one due notification per unit, per cycle |
| Deterioration | `apps/deterioration-worker.ts` | Up to 50 active monitors per unit per cycle, bounded to 2,000 encounter records of input |
| Coordination | `apps/coordination-worker.ts` | Up to 50 due notifications per tenant per pass, every ~10 seconds |

Each supports `--once` for externally-scheduled bounded invocation, and an
embedded `worker: true` profile flag for single-process development only.
**A scale-to-zero, request-billed web service is explicitly documented as
not a reliable background worker** for any of these — see
[docs/FOLLOW-UP.md](../FOLLOW-UP.md), [docs/INTEGRATIONS.md](../INTEGRATIONS.md),
[docs/DETERIORATION.md](../DETERIORATION.md).

## Leasing

Each worker claims work with an expiring lease; a new lease fences off a
worker that died mid-cycle. PostgreSQL concurrency/version checks prevent
duplicate alert/task commits across multiple worker replicas, although
replicas may still perform duplicate *inference* calls (an external model
request is not itself transactional) — this is an explicit, documented
residual behavior, not an oversight (docs/DETERIORATION.md).
