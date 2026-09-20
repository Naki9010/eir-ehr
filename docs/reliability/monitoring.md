# Monitoring

See [../architecture/observability.md](../architecture/observability.md) for
the full architectural treatment; this page focuses on the operational gap
for a real deployment.

## What exists

`/health`, `/ready`, worker last-check/freshness indicators surfaced in the
relevant workspace UI (follow-up, deterioration, integrations), and the
audit-chain startup check. See
[../architecture/observability.md](../architecture/observability.md).

## What a real deployment must add (not yet built here)

CLAUDE.md §110's list — error rate, latency, database health, queue
backlog, integration failures, authentication/authorization failures,
resource utilization, failed jobs, retry volume, external dependency
failures — none of this has a monitoring/alerting integration in this
repository. The in-UI "stale work" warnings are explicitly documented as
**not** a substitute: "A stale-work warning in the UI cannot detect an
outage when nobody opens the UI" (docs/REGULATORY-ALIGNMENT.md).

## Operational ownership is explicitly named as external to the software

Operators must monitor failed cycles, oldest pending work, quarantined
results, and overdue clinical tasks, with agreed response times and
telephone fallback — this is stated as a deployment responsibility in
[docs/INTEGRATIONS.md](../INTEGRATIONS.md) and
[docs/FOLLOW-UP.md](../FOLLOW-UP.md), not something the current codebase
automates.
