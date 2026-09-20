# Dependency Security

Primary source: `.github/workflows/ci.yml`, `package.json`, `package-lock.json`.

## CI enforcement

Every push/PR runs `npm audit --omit=dev --audit-level=high` as the final CI
step (`.github/workflows/ci.yml`) — a build fails on a high-or-above severity
vulnerability in a production dependency. `npm ci` (lockfile-exact install)
is used throughout, including in the Docker build
(`RUN npm ci --omit=dev --ignore-scripts`).

## Direct production dependencies (`package.json`)

`@fastify/cookie`, `@fastify/rate-limit`, `@fastify/static`,
`@js-temporal/polyfill`, `csv-parse`, `fastify`, `lucide`, `openid-client`,
`pdf-lib`, `personnummer`, `pg`, `tsx`, `zod`. Dev-only: `@playwright/test`,
`jose`, `prettier`, `typescript`, plus `@types/*`.

## Plugin trust, not sandboxing (see also [../architecture/system-overview.md](../architecture/system-overview.md))

A malicious or vulnerable plugin — whether from this repository or a
third-party module path an operator points a profile at — runs with full
application privileges. There is no dependency-injection-based sandbox;
`requires`/`provides` is a programming contract only. Pin dependencies and
review both source and transitive dependencies before enabling any
third-party plugin (docs/PLUGINS.md).

## Not implemented

No SBOM generation, no signed-distribution/provenance verification, and no
isolated runtime for untrusted extensions — all named as explicit future
work in docs/PLAN.md ("Open Governance": "Publish reviewed plugin versions
and hashes, dependency inventories and evaluation results").
