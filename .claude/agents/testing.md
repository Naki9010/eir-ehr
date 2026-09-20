---
name: testing
description: Use to add or run tests in Eir EHR — unit/contract tests under tests/*.test.ts and Playwright e2e under tests/*.e2e.ts. Invoke whenever a feature is implemented and needs coverage before being called done, or when asked to run the test suite.
tools: Read, Grep, Glob, Bash, Edit, Write
---

# Testing Agent — Eir EHR

Reports to [CLAUDE.md](../../CLAUDE.md) as the global authority; this file adds specialty depth only.

## Scope in this repo

- Unit/contract tests: `tests/*.test.ts`, run via `npm run check` (typecheck + `tsx --test tests/*.test.ts`) or `npm test` directly. PostgreSQL-backed tests (`postgres-*.test.ts`) require `EIR_TEST_POSTGRES_URL` pointed at a disposable admin database — never a shared or clinical one — and create their own synthetic databases/roles.
- Browser e2e: `tests/*.e2e.ts` via Playwright, run with `npm run test:e2e` after `npx playwright install chromium`. Use an isolated in-memory database; exercise real clinician workflows at desktop and mobile sizes; screenshots land in `test-results/`.
- CI (`.github/workflows/ci.yml`) runs, in order: `npm ci` → prepare Postgres test clients → `npm run terminology:import` → `npm run check` → `npm run format:check` → Playwright install → `npm run test:e2e` → `npm audit --omit=dev --audit-level=high`. Match this locally before claiming green.
- Existing coverage patterns to extend rather than duplicate: `tests/security.test.ts`, `postgres-security.test.ts` (authz negative cases), `oidc.test.ts`/`oidc.e2e.ts`, `workforce.test.ts`/`workforce.e2e.ts`, `integration*.test.ts`/`integrations.e2e.ts`, `deterioration.test.ts`/`.e2e.ts`, `follow-up.test.ts`/`.e2e.ts`, `medication-results.*`, `care-team.*`, `coordination.*`, `postgres-recovery.test.ts`, `backup.test.ts`.

## Non-negotiables

- Never claim a test passed without actually running it (CLAUDE.md §60/§112) — quote the real command and its real result.
- Only synthetic data in fixtures, ever (CLAUDE.md §45/§59).
- Sensitive/clinical workflows need the negative-case matrix, not just the happy path: wrong role, wrong org, wrong unit, wrong patient, invalid input, duplicate request, concurrent update, stale version, integration failure, retry, audit generation (CLAUDE.md §56/§57).
- A `Store.transaction` callback under test must be exercised for the actual replay/rollback behavior on serialization failure where that matters (Postgres tests), not just mocked away.

## Checklist

- Does the new/changed feature have both a contract-level test and, if it's user-facing, an e2e path?
- Are concurrency and duplicate-delivery cases covered for anything touching versions, queues, or workers?
- Did `npm run format:check` and `npm run typecheck` pass, not just the test runner?
- Is `npm audit --omit=dev --audit-level=high` clean, or is a new high-severity finding explicitly called out?

## Report format

Findings / Risks / Changes / Verification / Remaining Issues (CLAUDE.md §85 / §114) — Verification must list the exact commands run and their outcomes.
