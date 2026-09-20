# Failure Modes

Primary source: CLAUDE.md §43, [docs/OPERATIONS.md](../OPERATIONS.md).

Eir's async/queue design assumes, explicitly: network failure, database
timeout, queue failure, external service failure, duplicate events, delayed
events, partial writes, process restart, deployment interruption, stale
data, and concurrent requests.

| Failure | How it's handled |
| --- | --- |
| Process crash mid-transaction | Transaction rollback — no partial record (see [../database/transactions.md](../database/transactions.md)) |
| Process crash after commit, before external send | Outbox row remains `pending`; a new worker lease picks it up — no lost message, possible duplicate send if the crash happened *after* a successful send but *before* the ack was recorded (mitigated by idempotency keys, see [idempotency.md](idempotency.md)) |
| Worker crash mid-lease | Lease expires; a new worker claims the row — no permanent stuck state, bounded by the lease timeout |
| Database unreachable | `/ready` returns `503` without driver detail; writes fail closed, never silently degrade to a cached/guessed result |
| External service unreachable | Retry with backoff up to a bounded attempt budget, then a visible failed state requiring authorized replay |
| Concurrent conflicting writes | `409`, never last-write-wins (see [../database/concurrency.md](../database/concurrency.md)) |
| Duplicate inbound delivery | Deduplicated by message ID + payload hash; changed-content reuse of an ID is a `409`, not a silent overwrite |

## What "fail safely" means concretely, per domain

- Follow-up: a missing review disposition leaves the task **open**, never
  silently completed (see [../clinical/patient-safety.md](../clinical/patient-safety.md)).
- Deterioration: a model outage produces an `unavailable` assessment, never
  a reassuring "no risk" result (docs/DETERIORATION.md).
- Lab integration: a semantically unmatched result is quarantined, never
  fuzzy-matched into the wrong patient's chart (docs/INTEGRATIONS.md).
