# Secrets

Primary source: [docs/PERSISTENCE.md](../PERSISTENCE.md),
[docs/RECOVERY.md](../RECOVERY.md), [docs/INTEGRATIONS.md](../INTEGRATIONS.md),
[docs/FOLLOW-UP.md](../FOLLOW-UP.md).

## Rule: profiles reference secrets by environment-variable name, never by value

Every configuration profile (`eir.*.config.json`) that needs a credential
uses an `*Env` field (e.g. `clientSecretEnv`, `outboundTokenEnv`,
`inboundTokenEnv`, `tokenEnv`, `connectionStringEnv`) naming an environment
variable — the actual secret is injected at process start, never written
into JSON or committed to source control.

## Inventory of secret-bearing environment variables

| Variable pattern | Purpose |
| --- | --- |
| `clientSecretEnv` target (e.g. `EIR_OIDC_CLIENT_SECRET`) | OIDC client secret |
| `EIR_DATABASE_URL` / `connectionStringEnv` target | PostgreSQL runtime connection string |
| `EIR_POSTGRES_MIGRATION_URL` | Migration-only operator credential (distinct from the runtime role) |
| `EIR_LAB_ORDER_TOKEN` / `EIR_LAB_RESULT_TOKEN` (example names) | Outbound/inbound lab connector bearer tokens |
| `EIR_NOTIFICATION_TOKEN` (example name) | Follow-up/coordination notification gateway bearer token |
| `EIR_BACKUP_KEY` | 32-byte base64 AES-256-GCM key for encrypted logical backups |
| `EIR_BACKUP_DATABASE_URL` / `EIR_RESTORE_DATABASE_URL` | Backup/restore operator credentials, distinct from the runtime credential |

This repository's own `.env` (untracked, local-only) holds a development
staging admin password used solely to provision the local PostgreSQL
container in `compose.staging.yml`; it is not committed and this
documentation does not reproduce its value.

## Rotation and minimum strength

Lab/notification tokens: independently generated random base64url, minimum
32 bytes. Rotation requires coordinating both parties — there is no
overlapping-key grace period implemented, so rotation is a planned
maintenance action, not a live hot-swap.

## What is explicitly never done

Never hardcoded in source, never logged (server logging deliberately
excludes authorization headers, tokens, and credentials — CLAUDE.md §108),
never placed in a URL, and never returned by the FHIR export or any API
response. Backup encryption keys are managed by a secret manager, with no
key identifier stored alongside the archive — the operator must maintain
the archive-to-key-version inventory separately (docs/RECOVERY.md).
