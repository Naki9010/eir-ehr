---
description: Run Eir EHR's full applicable test suite (matching CI) and report real results only.
---

Run the applicable parts of Eir EHR's test suite, matching `.github/workflows/ci.yml` order, and report only what actually ran and its actual result (CLAUDE.md §94, §60 "Never claim success unless the commands actually succeeded"):

1. `npm run terminology:import` (required before tests that touch diagnosis codes).
2. `npm run check` — typecheck (`tsc --noEmit`) + unit/contract tests (`tsx --test tests/*.test.ts`). If `EIR_TEST_POSTGRES_URL` is unset, note explicitly that the PostgreSQL-backed tests (`postgres-*.test.ts`) were skipped or failed for that reason — do not silently ignore it.
3. `npm run format:check`.
4. `npx playwright install chromium` (if not already installed) then `npm run test:e2e`.
5. `npm audit --omit=dev --audit-level=high`.

If the task at hand only touches one subsystem, it's acceptable to run a narrower set (e.g. just `tests/integrations.test.ts` and `integrations.e2e.ts` for an integration change) — but say explicitly that the run was narrowed and why, rather than implying full CI coverage.

Report format: one line per command with its real exit status/summary, then an overall verdict. Do not paraphrase a failure as a pass. If something could not be run (missing `EIR_TEST_POSTGRES_URL`, no network for terminology import, etc.), state exactly what was not verified.
