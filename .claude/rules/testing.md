# Testing Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md) §46–§59. This file gives the testing agent the detailed shape.

## Commands

- `npm run check` — `tsc --noEmit` + `tsx --test tests/*.test.ts`.
- `npm test` — the same test run alone.
- `npm run test:e2e` — Playwright, requires `npx playwright install chromium` first.
- `npm run test:postgres` / the `postgres-*.test.ts` files inside `npm run check` — require `EIR_TEST_POSTGRES_URL` set to a disposable administrator database.
- `npm run format:check` / `npm run typecheck` — run separately in CI; don't assume `npm run check` covers formatting.
- `npm audit --omit=dev --audit-level=high` — part of the CI gate.

Match `.github/workflows/ci.yml`'s order when claiming "CI would pass": `npm ci` → prepare Postgres test clients → `npm run terminology:import` → `npm run check` → `npm run format:check` → Playwright install → `npm run test:e2e` → `npm audit`.

## Existing coverage to extend, not duplicate

Security/authz: `security.test.ts`, `postgres-security.test.ts`, `oidc.test.ts`/`oidc.e2e.ts`, `workforce.test.ts`/`workforce.e2e.ts`. Persistence: `postgres.test.ts`, `postgres-clinical.test.ts`, `postgres-lifecycle.test.ts`, `postgres-recovery.test.ts`, `postgres-startup.test.ts`, `postgres-workflow.e2e.ts`, `storage-async.test.ts`. Integrations: `integration.test.ts`, `integration-storage.test.ts`, `integrations.test.ts`, `integrations.e2e.ts`. Clinical modules: `clinical.test.ts`, `care-team.test.ts`/`.e2e.ts`, `coordination.test.ts`/`.e2e.ts`, `deterioration.test.ts`/`.e2e.ts`, `follow-up.test.ts`/`.e2e.ts`, `medication-results.test.ts`/`.e2e.ts`, `terminology.test.ts`. Recovery: `backup.test.ts`. Public/demo: `public.e2e.ts`, `public-demo.test.ts`, `social.e2e.ts`. AI: `plugins-ai.test.ts`, `risk-http.test.ts`.

When adding a feature, find the nearest existing test file above and extend its pattern before creating a new one.

## The negative-case matrix (CLAUDE.md §56/§57) — required for sensitive/clinical features

Authorized access, unauthenticated, wrong role, wrong organization, wrong unit, wrong patient, invalid input, duplicate request, concurrent request, stale version, integration failure, retry, audit generation, data-leakage check, privilege-escalation attempt. For clinical workflows specifically also: correct vs. wrong patient, correct vs. wrong encounter, finalized-data protection, amendment, correction, concurrent edit, failed DB write, failed external service, duplicate event, retry, recovery.

## Data discipline

Every fixture and seed is synthetic. `apps/seed.ts` and `scripts/*-sandbox.ts` show the existing pattern for generating realistic-but-fake data — follow it rather than inventing a new generator or, worse, hand-copying anything resembling a real record.

## Verification language

Report exactly which commands ran and their real result. "Implemented and unit-tested" is a valid, precise claim; "should work" or "this passes CI" without having run the commands is not (CLAUDE.md §60/§112).
