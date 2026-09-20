# Test Matrix (Feature × Test Type)

| Feature | Unit/contract | Integration (real PostgreSQL) | E2E |
| --- | --- | --- | --- |
| Clinical core (patient/encounter/note/observation/condition/allergy/task) | `clinical.test.ts` | `postgres-clinical.test.ts` | `workflow.e2e.ts` |
| Identity/access (OIDC, workforce, protected identity) | `workforce.test.ts`, `oidc.test.ts`, `security.test.ts` | `postgres-security.test.ts`, `postgres-lifecycle.test.ts`, `postgres-startup.test.ts` | `oidc.e2e.ts`, `workforce.e2e.ts` |
| Care team (scheduling, inbox, drafts) | `care-team.test.ts` | (covered via postgres-clinical fixtures) | `care-team.e2e.ts` |
| Medications and laboratory results | `medication-results.test.ts` | — | `medication-results.e2e.ts` |
| Laboratory integration runtime | `integrations.test.ts`, `integration-storage.test.ts` | (real PostgreSQL queue tests included in `integrations.test.ts`) | `integrations.e2e.ts` |
| Clinical follow-up | `follow-up.test.ts` | (real PostgreSQL worker tests included) | `follow-up.e2e.ts` |
| Deterioration monitoring | `deterioration.test.ts`, `risk-http.test.ts` | — | `deterioration.e2e.ts` |
| Eir Samverkan (coordination) | `coordination.test.ts` | (real PostgreSQL restart/concurrency tests included) | `coordination.e2e.ts` |
| Terminology (ICD-10-SE) | `terminology.test.ts` | — | (covered indirectly via `workflow.e2e.ts`) |
| AI proposal/review | `plugins-ai.test.ts` | — | (covered indirectly via `workflow.e2e.ts`) |
| Persistence/backup/recovery | `backup.test.ts`, `storage-async.test.ts` | `postgres.test.ts`, `postgres-recovery.test.ts`, `postgres-http.test.ts` | — |
| Public demo | `public-demo.test.ts` | — | `public.e2e.ts` |
| Social sharing metadata | — | — | `social.e2e.ts` |

This is a coverage map derived from file names and their documented
purpose in the flat `docs/*.md` sources — it is not a line-by-line
assertion audit. See [../traceability.md](../traceability.md) for the
requirement-to-test traceability view across the whole system.
