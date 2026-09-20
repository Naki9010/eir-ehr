# Testing Documentation

Primary source: `package.json` scripts, `.github/workflows/ci.yml`, the
`tests/` directory (8,279 total lines across its `*.test.ts`/`*.e2e.ts`
files as of this writing). See also
[.claude/rules/testing.md](../../.claude/rules/testing.md).

## Contents

- [strategy.md](strategy.md) — overall approach and what "passing" actually proves
- [unit-tests.md](unit-tests.md) — `tsx --test tests/*.test.ts`
- [integration-tests.md](integration-tests.md) — real-PostgreSQL and multi-plugin tests
- [api-tests.md](api-tests.md) — HTTP-contract-level tests
- [e2e-tests.md](e2e-tests.md) — Playwright browser workflows
- [security-tests.md](security-tests.md) — the CLAUDE.md §56 security matrix, as actually covered
- [clinical-safety-tests.md](clinical-safety-tests.md) — the CLAUDE.md §57 clinical matrix, as actually covered
- [test-matrix.md](test-matrix.md) — feature × test-type coverage table

## Commands

```sh
npm run check      # tsc --noEmit && tsx --test tests/*.test.ts
npm run test:e2e    # tsx --test tests/*.e2e.ts (Playwright)
npm run test:postgres  # real PostgreSQL provider/clinical/recovery suites
npm run format:check
```

CI (`.github/workflows/ci.yml`) runs, against a real PostgreSQL 18 service
container: `npm ci` → prepare real `pg_dump`/`pg_restore` clients from the
container → `npm run terminology:import` → `npm run check` →
`npm run format:check` → Playwright install → `npm run test:e2e` →
`npm audit --omit=dev --audit-level=high`.

## What test success does and does not prove

Passing tests are developer-controlled engineering evidence using synthetic
data. They demonstrate the code does what its tests assert — they do not
establish clinical effectiveness, regulatory approval, national-service
onboarding, or independent security certification
(docs/REGULATORY-ALIGNMENT.md "Evidence index"). Never claim a test was run
if it was not (CLAUDE.md §112).
