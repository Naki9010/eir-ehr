# CI/CD

Primary source: `.github/workflows/ci.yml`.

## What CI runs (every push and PR, `ubuntu-latest`, 10-minute timeout)

1. Checkout, Node 22 setup with npm cache.
2. `npm ci`.
3. Prepare real `pg_dump`/`pg_restore` clients from the running
   PostgreSQL 18 service container (`scripts/prepare-postgres-test-
   clients.ts`) — see [../testing/strategy.md](../testing/strategy.md).
4. `npm run terminology:import`.
5. `npm run check` (typecheck + `tsx --test tests/*.test.ts`).
6. `npm run format:check` (Prettier).
7. `npx playwright install --with-deps chromium`.
8. `npm run test:e2e`.
9. `npm audit --omit=dev --audit-level=high`.

A real PostgreSQL 18 service container backs the whole run
(`EIR_TEST_POSTGRES_URL` points at it), so PostgreSQL-dependent tests are
not skipped in CI.

## What CI does NOT do

There is no CD (continuous deployment) step in this workflow — deployment
to the public demo is a separate, manually-invoked script (`npm run
hosting:deploy`) or the Firebase CLI, not triggered automatically by CI. See
[deployment.md](deployment.md) and [docs/HOSTING.md](../HOSTING.md)
"Deployment order". There is no separate CI job/matrix for multiple Node
versions, multiple OSes, or a staging-environment smoke test triggered on
merge.
