# Configuration

Primary source: `package.json` `scripts`, the four `eir.*.config.json`
profiles, [docs/PERSISTENCE.md](../PERSISTENCE.md), [docs/RECOVERY.md](../RECOVERY.md).

## `npm run` scripts (full list, `package.json`)

`dev`, `staging`, `worker:integrations`, `worker:follow-up`,
`worker:deterioration`, `worker:coordination`, `demo:integrations`,
`demo:follow-up`, `db:migrate`, `test:postgres`, `backup:postgres`,
`backup:schedule`, `restore:postgres`, `terminology:import`, `start`,
`demo:public`, `hosting:prepare`, `social:generate`, `hosting:deploy`,
`test`, `typecheck`, `format`, `format:check`, `check`, `test:e2e`,
`plugins`, `backup`, `smoke:model`, `smoke:public`.

## Environment variables (non-secret configuration)

| Variable | Purpose |
| --- | --- |
| `EIR_DEMO` | Seeds patient records on local-server startup |
| `PORT` | Overrides the default loopback port |
| `EIR_CONFIG` | Path to an alternate plugin-composition profile |
| `EIR_SYNTHETIC_STAGING` | Enables the staging profile's seed data |
| `EIR_SANDBOX_PORT` / `EIR_LAB_SANDBOX_PORT` | Ports for the integrations demo sandbox |
| `EIR_FOLLOW_UP_PORT` | Port for the follow-up demo sandbox |
| `EIR_TEST_POSTGRES_URL` | Disposable admin database URL for PostgreSQL-backed tests |
| `EIR_DEMO_TEST_URL` | Target URL for browser tests run against a deployed instance |
| `EIR_PUBLIC_ORIGINS` | Allowed HTTPS origins for the public demo |

See [../security/secrets.md](../security/secrets.md) for the parallel list
of **secret-bearing** environment variables (`*Env`-named fields, backup
keys, database URLs) — configuration and secrets are deliberately separated
in this table because they have different handling rules.

## Profile files are never secrets themselves

`eir.config.json`, `eir.demo.config.json`, `eir.staging.config.json`, and
`eir.clinic.config.example.json` are committed to the repository — they
contain plugin composition and non-secret settings only, referencing
secrets by environment-variable name (see [../security/secrets.md](../security/secrets.md)).
A real clinic deployment copies `eir.clinic.config.example.json`, replaces
every placeholder, and keeps its own copy **outside** version control if it
ever contains deployment-specific non-public detail.
