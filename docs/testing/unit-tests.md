# Unit / Contract Tests

Run via `npm test` (`tsx --test tests/*.test.ts`), part of `npm run check`.

## Representative files (from `tests/`)

`clinical.test.ts`, `workforce.test.ts`, `terminology.test.ts`,
`medication-results.test.ts`, `care-team.test.ts`, `follow-up.test.ts`,
`deterioration.test.ts`, `coordination.test.ts`, `integrations.test.ts`,
`integration-storage.test.ts`, `security.test.ts`, `oidc.test.ts`,
`risk-http.test.ts`, `storage-async.test.ts`, `plugins-ai.test.ts`,
`backup.test.ts`, `public-demo.test.ts`, and the `postgres-*.test.ts` family
(`postgres.test.ts`, `postgres-clinical.test.ts`, `postgres-security.test.ts`,
`postgres-lifecycle.test.ts`, `postgres-startup.test.ts`,
`postgres-http.test.ts`, `postgres-recovery.test.ts`).

Shared fixtures/helpers: `helpers.ts`, `workforce-helpers.ts`,
`integration-helpers.ts`, `follow-up-helpers.ts`, `postgres-helpers.ts`,
`postgres-clinical-helpers.ts`, `oidc-provider.ts` (a real loopback test
OIDC provider, not a mock).

## What these prove

Each domain plugin's contract behavior against its own test fixtures,
including negative cases (wrong tenant, wrong role, stale version, missing
dependency) per the plugin-replacement test checklist in
[docs/PLUGINS.md](../PLUGINS.md) "Required tests for a replacement".
