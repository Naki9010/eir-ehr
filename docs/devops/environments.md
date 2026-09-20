# Environments

Primary source: [../architecture/system-overview.md](../architecture/system-overview.md)
"Composition roots", `package.json`.

| Profile | Entry point | Storage | Identity | Purpose |
| --- | --- | --- | --- | --- |
| `eir.config.json` | `apps/server.ts` (`npm run dev` / `npm start`) | SQLite (`.data/ehr.sqlite`) | `identity-local.ts` | Legacy local development only |
| `eir.demo.config.json` | `apps/public-server.ts` (`npm run demo:public`) | SQLite, one **in-memory** database per visitor | `identity-staff-local.ts`, `localDevelopmentOnly: true` | Disposable public demo (also the Docker image's default command) |
| `eir.staging.config.json` | `apps/staging-server.ts` (`npm run staging`) | PostgreSQL (`compose.staging.yml`) | Local synthetic | Persistent synthetic staging, tenant fixed to `eir-synthetic-staging` |
| `eir.clinic.config.example.json` | Not wired to a specific `npm run` script — copy and adapt | SQLite by default; swap to `plugins/storage-postgres.ts` per [docs/PERSISTENCE.md](../PERSISTENCE.md) | `identity-oidc.ts` | Template for a real clinic deployment — placeholders must be replaced |

Every profile is a JSON array of `{module, config}` plugin entries loaded
by `packages/runtime.ts`'s `fromConfig` — see
[../architecture/system-overview.md](../architecture/system-overview.md).
`EIR_CONFIG=/absolute/path/to/profile.json` overrides which profile a given
`npm run` script loads (used for the worker processes, which are not tied
to one fixed profile).

## Worker-only environments

`apps/integration-worker.ts`, `apps/follow-up-worker.ts`,
`apps/deterioration-worker.ts`, `apps/coordination-worker.ts` each load a
caller-supplied profile via `EIR_CONFIG` and run standalone, independent of
any HTTP server process — see
[../reliability/queues.md](../reliability/queues.md).
