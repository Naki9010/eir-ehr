# Logging

Primary source: [docs/OPERATIONS.md](../OPERATIONS.md), [docs/API.md](../API.md),
CLAUDE.md §108.

## What is logged

Fastify's default structured request logging, deliberately configured to
exclude request bodies, authorization headers, identifiers, and record
content. Recovery/backup tooling logs are even stricter: CLI messages for
`scripts/postgres-backup.ts`/`postgres-restore.ts` never include SQL, row
values, tokens, URLs, keys, or raw driver errors — even a client stderr
warning is treated as a failure rather than partially logged
(docs/RECOVERY.md).

## What is deliberately never logged

Names, personnummer, clinical notes, lab results, medication details,
tokens, or credentials (CLAUDE.md §108's list, matched by the actual
backup/restore and API logging behavior described above).

## Gap

There is no centralized log-aggregation, correlation-ID propagation, or
log-review tooling verified in this repository — see
[../architecture/observability.md](../architecture/observability.md) and
[../reliability/monitoring.md](../reliability/monitoring.md). A privileged
host administrator can still inspect process environments; recovery tooling
explicitly recommends running under a dedicated trusted OS account
(docs/RECOVERY.md).
