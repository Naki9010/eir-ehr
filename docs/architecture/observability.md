# Architecture: Observability

## What exists today

- `GET /health` — liveness.
- `GET /ready` — checks the database and returns `503` without driver
  details when unavailable (docs/PERSISTENCE.md).
- `GET /api/plugins` — active plugin IDs, versions, and declared service
  dependencies (useful to confirm what composition is actually running).
- The audit chain (`Store.verifyAudit()`) is checked on startup.
- Worker cycles expose a last-check/freshness signal in their respective
  workspace UIs (follow-up, deterioration, integrations) — documented
  explicitly as an operational signal, not an uptime guarantee or paging
  system (docs/FOLLOW-UP.md, docs/DETERIORATION.md).
- Structured logs deliberately exclude request bodies, authorization
  headers, identifiers, and clinical content (docs/OPERATIONS.md, CLAUDE.md
  §108).

## What does not exist (gap)

There is no metrics/tracing exporter (e.g. OpenTelemetry, Prometheus),
correlation-ID propagation contract from browser through API through
queue/integration boundary, centralized log aggregation, or alerting
integration verified in this repository. CLAUDE.md §42 describes the target
(API latency/error rate, database health, queue health, integration/auth
failures, correlation IDs frontend→API→service→database→queue→integration);
none of this is implemented today. This is a documented gap, not an
oversight to route around silently — see
[../reliability/monitoring.md](../reliability/monitoring.md) for the
inventory of what an operator must add before a pilot deployment.
