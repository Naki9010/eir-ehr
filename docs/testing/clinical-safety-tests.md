# Clinical Safety Tests

The CLAUDE.md §57 clinical-safety test matrix, mapped to actual coverage:

| Scenario | Covered by |
| --- | --- |
| Correct patient / wrong patient | `security.test.ts`, `clinical.test.ts` |
| Correct / wrong encounter | `clinical.test.ts` (open-encounter reference checks) |
| Finalized data (signed note immutability) | `clinical.test.ts`, `postgres-clinical.test.ts` (DB-trigger-level) |
| Amendment | `clinical.test.ts` |
| Correction | `clinical.test.ts` (observations/conditions/allergies `entered-in-error`) |
| Concurrent edit / stale data | `postgres-clinical.test.ts`, `care-team.test.ts` |
| Failed database write | `postgres-clinical.test.ts`, `medication-results.test.ts` (transaction-rollback-on-audit-failure test) |
| Failed external service | `integrations.test.ts`, `follow-up.test.ts` |
| Duplicate event | `integrations.test.ts`, `medication-results.test.ts` (message/clientId replay) |
| Retry | `integrations.test.ts`, `follow-up.test.ts` |
| Audit | `postgres-security.test.ts` |
| Recovery | `postgres-recovery.test.ts`, `backup.test.ts` |

Deterioration-specific safety: `deterioration.test.ts`/`deterioration.e2e.ts`
verify an alert never auto-resolves from a lower subsequent value, a
corrected observation, missing data, or a model outage — only an explicit
`resolve` action against the current assessment ID (see
[docs/DETERIORATION.md](../DETERIORATION.md)).

Follow-up-specific safety: `follow-up.test.ts` verifies a missing
disposition fails safe to **open**, never silently closed (see
[../clinical/patient-safety.md](../clinical/patient-safety.md)).

Lab-review-specific safety: `medication-results.test.ts` verifies generic
task completion/cancellation cannot bypass required report review (see
[../clinical/laboratory-results.md](../clinical/laboratory-results.md)).
