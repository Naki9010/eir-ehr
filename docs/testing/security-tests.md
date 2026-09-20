# Security Tests

The CLAUDE.md §56 security test matrix, mapped to actual coverage:

| Scenario | Covered by |
| --- | --- |
| Normal authorized access | Every domain test file's happy path |
| Unauthenticated access | `security.test.ts` |
| Wrong role | `workforce.test.ts`, `security.test.ts` |
| Wrong organization / wrong unit | `postgres-security.test.ts` (RLS/tenant isolation), `workforce.test.ts` |
| Wrong patient | `security.test.ts`, clinical/medication/lab test files |
| Invalid input | Per-domain schema-validation assertions throughout `tests/*.test.ts` |
| Duplicate request | `integrations.test.ts` (message replay), `care-team.test.ts` (idempotent drafts), medication/lab `clientId` replay tests |
| Concurrent request | `postgres-clinical.test.ts`, `care-team.test.ts` (overlap/booking races) |
| Stale version | Version-conflict (`409`) assertions across clinical/medication/lab/care-team tests |
| Integration failure | `integrations.test.ts`, `follow-up.test.ts` (delivery failure/retry) |
| Retry | `integrations.test.ts`, `follow-up.test.ts`, `coordination.test.ts` |
| Audit generation | `postgres-security.test.ts`, `backup.test.ts` (audit-chain-survives-restore) |
| Data leakage | Visibility-filter assertions in medication/lab/coordination test files (patient/proxy exclusion) |
| Privilege escalation | `workforce.test.ts` (self-grant prevention, cross-unit denial) |

This table is a coverage map, not a claim that every cell has exhaustive
coverage for every endpoint — see
[clinical-safety-tests.md](clinical-safety-tests.md) and
[test-matrix.md](test-matrix.md) for the feature-level breakdown, and treat
any endpoint not clearly traceable to a row above as a documentation gap to
close, not as implicitly covered.
