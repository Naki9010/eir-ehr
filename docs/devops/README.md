# DevOps Documentation

Primary source: `Dockerfile`, `compose.staging.yml`,
`.github/workflows/ci.yml`, [docs/HOSTING.md](../HOSTING.md),
[docs/PERSISTENCE.md](../PERSISTENCE.md). See also
[.claude/agents/devops.md](../../.claude/agents/devops.md).

## Contents

- [environments.md](environments.md) — the four profiles and what each is for
- [local-development.md](local-development.md) — running Eir locally
- [docker.md](docker.md) — the container build (public demo only)
- [ci-cd.md](ci-cd.md) — `.github/workflows/ci.yml`
- [deployment.md](deployment.md) — the actual public-demo deployment topology
- [configuration.md](configuration.md) — profile files and environment variables
- [rollback.md](rollback.md) — what rollback means for each environment

## Scope note

Only the **public demo** has a documented, scripted deployment (Cloud
Run + Firebase Hosting). There is no documented, scripted production
clinic-deployment pipeline — `eir.clinic.config.example.json` is explicitly
a template requiring an operator to replace placeholders, provision
infrastructure, and review every setting before use.
