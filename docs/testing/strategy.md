# Testing Strategy

Primary source: CLAUDE.md §55–§58, `.github/workflows/ci.yml`.

## Runner

No separate test framework dependency — tests use Node's built-in test
runner via `tsx --test`, directly against TypeScript source (no compile
step). Unit/integration/API tests are `tests/*.test.ts`; browser end-to-end
tests are `tests/*.e2e.ts`, run via `@playwright/test`.

## The PostgreSQL-in-CI decision

CI runs a real PostgreSQL 18 service container and prepares real
`pg_dump`/`pg_restore` clients from it (`scripts/prepare-postgres-test-
clients.ts`) rather than mocking the database — tests that require it
(`postgres-*.test.ts`, `backup.test.ts`'s real-drill path) exercise actual
transactions, actual RLS, and an actual encrypted backup/restore round trip
against a disposable database. This directly follows the project's stated
principle of not mocking the database for things that must behave
correctly under real concurrency and real constraints (see
[integration-tests.md](integration-tests.md)).

## What "covered" means per risk tier

High/critical-risk areas (CLAUDE.md §60 — medications, lab results, notes,
patient matching, patient identity, security boundaries) have dedicated
test files rather than being folded into a general suite: e.g.
`medication-results.test.ts`, `security.test.ts`, `postgres-security.test.ts`,
`workforce.test.ts`, `oidc.test.ts`. See [test-matrix.md](test-matrix.md).

## What is not covered by this test suite

Load/soak testing, independent penetration testing, WCAG conformance
testing, and clinical-effectiveness evaluation are all explicitly named as
**not** part of this automated suite and remain open gates before a
clinical pilot (docs/PLAN.md "M4: operational pilot gate", docs/
REGULATORY-ALIGNMENT.md "Clinical release gates").
