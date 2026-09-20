# Traceability Matrix

Requirement → Feature → Architecture → API → Database → Security → Clinical
Safety → Tests → Release, for the significant capabilities in this
repository. This is derived from the flat `docs/*.md` sources and the test
files that actually exist — see each linked page for full detail.

| Requirement | Feature | Architecture | API | Database | Security | Clinical Safety | Tests | Release |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Register a patient and maintain a chart | [patient-registration-and-chart](features/patient-registration-and-chart.md) | [system-overview](architecture/system-overview.md) | `/patients`, `/patients/:id/chart` | `entities` (kind=patient) | `patient.register`, `chart.read` | Wrong-patient tests, no-merge policy ([ADR-0005](decisions/ADR-0005-no-patient-merge.md)) | `clinical.test.ts`, `workflow.e2e.ts` | Not yet tagged — see [releases/](releases/README.md) |
| Draft, sign, and amend clinical notes | [clinical-notes](clinical/clinical-notes.md) | [backend](architecture/backend.md) | `/records/:id/save\|sign\|amend` | `entities`, `versions` | `note.sign`, `record.write` | Immutability via DB trigger ([ADR-0004](decisions/ADR-0004-note-immutability-via-db-trigger.md)) | `clinical.test.ts`, `postgres-clinical.test.ts` | — |
| Document and reconcile medication use | [medication-reconciliation](features/medication-reconciliation.md) | [backend](architecture/backend.md) | `/patients/:id/medications`, `/medication-reviews` | `entities` (medicationStatement, review) | `medication.write`, `medication.reconcile` | Stale-reconciliation display, no-restore-voided rule | `medication-results.test.ts`, `.e2e.ts` | — |
| Order, receive, and review laboratory results | [laboratory-integration](features/laboratory-integration.md) | [reliability/queues](reliability/queues.md) | `/lab-orders`, `/integrations/:id/results` | Queue tables (migration v2) | `lab.order`, `lab.receive`, `lab.review`, `integration.manage` | Linked-task review-bypass guard | `integrations.test.ts`, `.e2e.ts`, `integration-storage.test.ts` | — |
| Track follow-up to completion, not just delivery | [follow-up](features/follow-up.md) | [reliability/queues](reliability/queues.md) | `/follow-up*` | `entities` (task, coverage, notification) | `task.write` | Fail-safe-to-open disposition rule | `follow-up.test.ts`, `.e2e.ts` | — |
| Monitor for deterioration with clinician response | [deterioration-monitoring](features/deterioration-monitoring.md) | [system-overview](architecture/system-overview.md) | `/modules`, `/deterioration*` | `entities` (monitoring, alert, assessment) | `modules.manage` | No-auto-resolve rule | `deterioration.test.ts`, `.e2e.ts`, `risk-http.test.ts` | — |
| Coordinate care across units with consent | [samverkan-care-coordination](features/samverkan-care-coordination.md) | [system-overview](architecture/system-overview.md) | `/coordination*` | `entities` (sam*-prefixed) | `coordination.*` | Discharge application guards | `coordination.test.ts`, `.e2e.ts` | — |
| Schedule, check in, and hand over care-team work | [care-team-scheduling](features/care-team-scheduling.md) | [backend](architecture/backend.md) | `/care-team`, `/appointments/:id/:action` | `entities` (appointment, task) | `schedule.write`, `task.write` | Booked-clinician-only start; atomic encounter completion | `care-team.test.ts`, `.e2e.ts` | — |
| Authenticate and authorize staff | [authentication](security/authentication.md), [authorization](security/authorization.md) | [system-overview](architecture/system-overview.md) | `/session`, `/workforce*`, `/access-review*` | `sessions`, `login_transactions` | Full 24-permission model | Protected-identity enforcement, emergency-access time-box | `workforce.test.ts`, `oidc.test.ts`, `security.test.ts`, `postgres-security.test.ts` | — |
| Persist and isolate tenant data reliably | [tenant-isolation](security/tenant-isolation.md) | [database](architecture/database.md) | (all `/api` routes, indirectly) | Full PostgreSQL schema, FORCE RLS ([ADR-0006](decisions/ADR-0006-postgres-rls-tenant-isolation.md)) | Role-bound RLS, distinct migration credential | — | `postgres*.test.ts` family | — |
| Back up and recover clinical data | [disaster-recovery](reliability/disaster-recovery.md) | [database](architecture/database.md) | — | Encrypted logical backup/restore | Fenced, superuser-gated restore | Restored-record/audit verification | `backup.test.ts`, `postgres-recovery.test.ts` | — |
| Generate reviewable AI proposals, never auto-signed | [ai-assisted-documentation](features/ai-assisted-documentation.md) | [ai/architecture](ai/architecture.md) | `/patients/:id/ai`, `/proposals/:id/review` | `entities` (proposal) | `ai.use` | Draft-only acceptance, stale-context recheck | `plugins-ai.test.ts` | — |
| Export clinical data as FHIR | [fhir/resources](interoperability/fhir/resources.md) | [ADR-0002](decisions/ADR-0002-fhir-projection-not-server.md) | `/patients/:id/export/fhir` | (reads existing entities) | Same as chart read | Visibility filtering applies identically | Cross-cutting: `medication-results.test.ts`, `terminology.test.ts`, `security.test.ts`, `integration.test.ts` | — |
| Import and apply Swedish diagnosis terminology | [diagnoses](clinical/diagnoses.md) | [system-overview](architecture/system-overview.md) | `/terminology/diagnoses` | (condition entities, versioned coding) | `chart.read` (search), `record.write` (save) | No silent recoding of history | `terminology.test.ts` | — |

## Maintenance rule

Update this matrix when a significant new feature is added, per the root
[CLAUDE.md](../CLAUDE.md) "Documentation Is Part Of The Code" rule and
CLAUDE.md §20 (Traceability Matrix). A feature without a traceable row here
is a documentation gap, not evidence the feature is untested — check
[testing/test-matrix.md](testing/test-matrix.md) and the relevant
[features/](features/README.md) doc before assuming a gap in this table
means a gap in coverage.
