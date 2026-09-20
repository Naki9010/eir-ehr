# Changelog

Reconstructed from repository commit history (`git log`) and the detailed
evidence in the flat `docs/*.md` files, which remain the authoritative
detail for each entry below. Dates are commit dates. This log starts at the
point this documentation system was introduced; earlier project history is
summarized from `git log` where a specific date/commit is available.

## 2026-09-19 — Allow CI budget for delayed autosave recovery

### Type
Infrastructure

### Summary
Adjusted CI timing tolerance for the care-team autosave-recovery test.

### Reason
Autosave recovery involves real debounce/network timing (see
[../frontend/forms.md](../frontend/forms.md)); CI runners need enough
budget for that timing to be reliably observed without flaking.

### Affected Areas
Testing, CI/CD

### Files Changed
CI test timing configuration (see commit `277e313`).

### Testing
`npm run test:e2e` in CI.

### Verification
CI green on `.github/workflows/ci.yml`.

### Remaining Risks
None specific to this change beyond ordinary CI flakiness risk.

---

## 2026-09-19 — Stabilize assignment test and dependency audit

### Type
Bug Fix / Security

### Summary
Fixed a flaky workforce-assignment test and addressed a dependency-audit
finding.

### Reason
Reliable CI is a precondition for trusting every other test result in this
suite (see [../testing/strategy.md](../testing/strategy.md)); dependency
findings are triaged as part of the `npm audit --omit=dev --audit-level=high`
CI gate (see [../security/dependency-security.md](../security/dependency-security.md)).

### Affected Areas
Testing, Security, Dependencies

### Files Changed
Workforce test fixture; `package-lock.json` (commit `44150a9`).

### Security Impact
Resolved an audit-flagged dependency issue at or above the CI's `high`
severity threshold.

### Testing
`npm run check`, `npm audit --omit=dev --audit-level=high`.

### Verification
CI green.

### Remaining Risks
None recorded beyond the ongoing need to keep dependencies current.

---

## 2026-09-19 — Add modular care coordination and SIP workflows (Eir Samverkan)

### Type
Feature

### Summary
Added shared cross-unit cases, consent, messages/receipts, structured SIP
(samordnad individuell plan), attachments/PDF, payment estimation, and
notifications — Eir's own implementation, explicitly not SAMSA. See
[docs/SAMVERKAN.md](../SAMVERKAN.md) and
[../features/samverkan-care-coordination.md](../features/samverkan-care-coordination.md).

### Reason
Extends the clinical workflow to multi-unit coordination (hospital /
primary care / municipality), a named requirement for realistic Swedish
care pathways, while deliberately keeping the deployment boundary at "one
tenant" rather than claiming regional federation.

### Affected Areas
Backend, Database, API, Security, Privacy, Clinical workflow, UX

### Files Changed
`plugins/coordination.ts`, `plugins/coordination-directory.ts`,
`plugins/coordination-documents.ts`, `plugins/coordination-notifications.ts`,
`plugins/coordination-payment.ts`, `plugins/sip-plans.ts`,
`packages/coordination.ts`, `apps/web/coordination-workspace.js`,
`apps/coordination-worker.ts`.

### Database Changes
New private `sam*`-prefixed entity kinds using the existing generic entity
schema — no destructive migration (see [../database/entities.md](../database/entities.md)).

### API Changes
New `/coordination*` routes, conditional on installed providers (see
[../interoperability/integrations/README.md](../interoperability/integrations/README.md)).

### Security Impact
New `coordination.*` permission family; attachments quarantined by default;
PDF parsing bounded and off the HTTP event loop.

### Privacy Impact
Private coordination records excluded from chart/history/change-feed/FHIR
paths by design.

### Clinical Safety Impact
Discharge requires a fixed primary-care contact and confirmed outpatient
availability (application guard, not a clinical decision engine).

### Interoperability Impact
No SAMSA import/export, no FHIR CarePlan/Communication/Consent mapping.

### Testing
`tests/coordination.test.ts`, `tests/coordination.e2e.ts`.

### Verification
Tests pass in CI against real PostgreSQL and SQLite.

### Remaining Risks
Regional acceptance, SITHS/HSA onboarding, approved payment rates, and
production document scanning remain open — see
[docs/SAMVERKAN.md](../SAMVERKAN.md) "Next Release Gates".

---

## 2026-09-19 — Add optional deterioration monitoring and unit-level module controls

### Type
Feature

### Summary
Added an optional, per-unit-activated vital-sign/trend monitoring module
with a replaceable rules engine or external HTTP model adapter, and the
`modules` activation mechanism used by it. See
[docs/DETERIORATION.md](../DETERIORATION.md) and
[../features/deterioration-monitoring.md](../features/deterioration-monitoring.md).

### Reason
Adds clinician-facing early-warning support while explicitly avoiding an
unvalidated-model-as-fact failure mode — architecturally referenced from
Verma et al., CMAJ 2024 (CHARTwatch), without claiming to reproduce that
model.

### Affected Areas
Backend, Database, API, Clinical workflow, Reliability

### Files Changed
`plugins/modules.ts`, `plugins/deterioration.ts`, `plugins/risk-vitals.ts`,
`plugins/risk-http.ts`, `packages/deterioration.ts`, `packages/modules.ts`,
`apps/web/deterioration-workspace.js`, `apps/web/module-workspace.js`,
`apps/deterioration-worker.ts`.

### Database Changes
New monitoring/assessment/alert/response-event entity kinds; no destructive
migration.

### API Changes
New `/modules`, `/deterioration*`, `/patients/:id/monitoring`,
`/monitoring/:id/*`, `/deterioration-alerts/:id/respond` routes.

### Clinical Safety Impact
An alert never auto-resolves from a lower value, correction, missing data,
or model outage — only an explicit `resolve` action. `no-trigger` never
means "low risk." Clinical activation is blocked by default in the clinic
example profile.

### Interoperability Impact
`eir.risk.v1` — not CDS Hooks, not FHIR RiskAssessment.

### Testing
`tests/deterioration.test.ts`, `tests/deterioration.e2e.ts`,
`tests/risk-http.test.ts`.

### Verification
Tests pass; bundled rules explicitly labelled as development fixtures in
UI/docs, not a validated score.

### Remaining Risks
No local retrospective/prospective evaluation, no calibration, no
independent clinical validation — see
[docs/DETERIORATION.md](../DETERIORATION.md) "Before clinical use".

---

## 2026-09-19 — Add clinical follow-up workflows and regulatory evidence brief

### Type
Feature

### Summary
Added the follow-up module (deadline tracking, coverage, escalation,
durable notification) and published
[docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md) as an evidence
brief for Swedish healthcare/regulatory stakeholders. See
[docs/FOLLOW-UP.md](../FOLLOW-UP.md) and
[../features/follow-up.md](../features/follow-up.md).

### Reason
Closes the gap between "result received" and "action completed" identified
as a patient-safety priority, and makes the project's actual regulatory
posture (not aspirational) explicit for stakeholder review.

### Affected Areas
Backend, Database, API, Clinical workflow, Reliability, Documentation

### Files Changed
`plugins/follow-up.ts`, `plugins/follow-up-policy.ts`,
`plugins/notification-http.ts`, `packages/follow-up.ts`,
`apps/web/follow-up-workspace.js`, `apps/follow-up-worker.ts`,
`docs/REGULATORY-ALIGNMENT.md`.

### Database Changes
New coverage/notification/event entity kinds; `searchEntities` gains
optional bounded `statuses` filtering in both storage providers.

### API Changes
New `/follow-up*` routes (see [../api/endpoints.md](../api/endpoints.md)).

### Clinical Safety Impact
A missing review disposition fails safe to **open**, never silently
completed.

### Testing
`tests/follow-up.test.ts`, `tests/follow-up.e2e.ts`.

### Verification
Tests pass; regulatory brief reviewed against primary Swedish/EU sources
(see [docs/SOURCES.md](../SOURCES.md)).

### Remaining Risks
Clinically approved timing/escalation policy per deployment and staffed
fallback remain open — see
[docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md) "Clinical
release gates".

---

## 2026-09-19 — Add reliable laboratory integration runtime and operator workflows

### Type
Feature / Reliability

### Summary
Added the durable lab-order-dispatch/result-intake runtime (`eir.lab.v1`),
leased-worker retry/reconciliation, and the operator integration workspace.
See [docs/INTEGRATIONS.md](../INTEGRATIONS.md) and
[../features/laboratory-integration.md](../features/laboratory-integration.md).

### Reason
Replaces ad hoc manual result entry with a durable, idempotent, auditable
pipeline suitable for eventually connecting a real laboratory partner.

### Affected Areas
Backend, Database, API, Security, Reliability, Interoperability

### Files Changed
`plugins/integrations.ts`, `plugins/lab-transport-http.ts`,
`packages/integrations.ts`, `packages/lab-application.ts`,
`apps/web/integration-workspace.js`, `apps/integration-worker.ts`.

### Database Changes
New queue/outbox/inbox tables/indexes (PostgreSQL migration v2 — see
[../database/migrations.md](../database/migrations.md)).

### API Changes
New `POST /api/patients/:id/lab-orders` (`connectorId`),
`GET /api/lab-connectors`, `GET /api/lab-orders/:id/delivery`,
`POST /integrations/:connectorId/results` (outside `/api`).

### Security Impact
New `integration.manage` permission, separate machine-credential scoping
per connector (see [../interoperability/integrations/authentication.md](../interoperability/integrations/authentication.md)).

### Interoperability Impact
`eir.lab.v1` is Eir's own protocol — not HL7, not an Inera contract.

### Testing
`tests/integrations.test.ts`, `tests/integration-storage.test.ts`,
`tests/integrations.e2e.ts`; local sandbox via `npm run demo:integrations`.

### Verification
Tests pass against real PostgreSQL and SQLite; local sandbox round trip
verified.

### Remaining Risks
No contracted Swedish laboratory connection — see
[docs/INTEGRATIONS.md](../INTEGRATIONS.md) "next external milestone".

---

## 2026-09-19 — Fix PostgreSQL client path in GitHub job environment

### Type
Bug Fix / Infrastructure

### Summary
Corrected how CI locates `pg_dump`/`pg_restore` binaries prepared from the
PostgreSQL 18 service container.

### Reason
The encrypted-backup test drill (`tests/backup.test.ts`) requires real,
version-matched PostgreSQL client binaries — see
[../reliability/disaster-recovery.md](../reliability/disaster-recovery.md).

### Affected Areas
CI/CD, Reliability (backup testing)

### Files Changed
`.github/workflows/ci.yml`, `scripts/prepare-postgres-test-clients.ts`.

### Testing
`npm run test:postgres` in CI.

### Verification
CI green.

### Remaining Risks
None recorded.

---

## 2026-09-19 — Add persistent PostgreSQL clinic backend and recovery

### Type
Feature / Database / Security

### Summary
Added the PostgreSQL storage provider (runtime API v2, async storage
contract), role-bound tenant isolation via RLS, guarded operator-run
migrations, persistent synthetic staging, and encrypted logical
backup/restore tooling. See [docs/PERSISTENCE.md](../PERSISTENCE.md) and
[docs/RECOVERY.md](../RECOVERY.md).

### Reason
SQLite alone cannot support a multi-process, persistent, tenant-isolated
clinical deployment; this milestone is the P0 persistence foundation named
in [docs/PLAN.md](../PLAN.md).

### Affected Areas
Backend, Database, Security, Reliability, DevOps

### Files Changed
`plugins/storage-postgres.ts`, `packages/postgres-migrations.ts`,
`packages/backup.ts`, `scripts/migrate-postgres.ts`,
`scripts/postgres-backup.ts`, `scripts/postgres-backup-loop.ts`,
`scripts/postgres-restore.ts`, `apps/staging-server.ts`,
`compose.staging.yml`, `eir.staging.config.json`.

### Database Changes
Full PostgreSQL schema with FORCE RLS on every provider-data table; a
checksum-verified, operator-only migration ledger (see
[../database/migrations.md](../database/migrations.md)).

### Security Impact
Runtime accounts are refused privileged rights (no superuser/BYPASSRLS/
ownership); migration credentials are explicitly distinct from runtime
credentials (see [../security/tenant-isolation.md](../security/tenant-isolation.md)).

### Interoperability Impact
None directly — storage-layer change, contracts unchanged for consumers.

### Testing
`tests/postgres.test.ts`, `tests/postgres-clinical.test.ts`,
`tests/postgres-security.test.ts`, `tests/postgres-lifecycle.test.ts`,
`tests/postgres-startup.test.ts`, `tests/postgres-http.test.ts`,
`tests/postgres-recovery.test.ts`, `tests/backup.test.ts`.

### Verification
Tests pass against real disposable PostgreSQL databases in CI; a full
encrypted backup/restore drill verified against real EHR records
(signed note, lab order, assignment, audit chain).

### Remaining Risks
No point-in-time recovery, no off-host immutable storage, no managed key
custody — see [../reliability/disaster-recovery.md](../reliability/disaster-recovery.md).

---

## 2026-09-19 — Add staff identity, clinic authorization and access review

### Type
Feature / Security

### Summary
Added the OIDC identity adapter, unit/assignment/action-level authorization
policy, staff revocation, protected-record exclusion, and the audit review
UI. See [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md).

### Reason
Named as the M1 milestone precondition in [docs/PLAN.md](../PLAN.md) —
real professional identity and a granular authorization policy are
prerequisites for any clinical-data handling, ahead of persistence work.

### Affected Areas
Backend, Security, Database, API, Clinical workflow

### Files Changed
`plugins/identity-oidc.ts`, `plugins/access-clinic.ts`,
`plugins/access-review.ts`, `plugins/workforce.ts`,
`packages/workforce.ts`, `packages/staff-sessions.ts`,
`apps/web/access-workspace.js`.

### Database Changes
Session/login-transaction schema v2 (inactivity tracking, durable login
transactions) — see [../database/migrations.md](../database/migrations.md).

### API Changes
New `POST /session/assignment`, `GET/POST /workforce`,
`POST /workforce/:id`, `GET/POST /access-review`,
`POST /patients/:id/emergency-access`, `POST /patients/:id/protection`,
`GET /patients/:id/permissions`.

### Security Impact
Full 24-permission model introduced (see
[../architecture/authorization.md](../architecture/authorization.md));
emergency access is time-boxed, read-only, and audited.

### Privacy Impact
`patient.protected` enforcement across directory, chart, export, and AI.

### Testing
`tests/workforce.test.ts`, `tests/oidc.test.ts`, `tests/security.test.ts`,
`tests/postgres-security.test.ts`, `tests/workforce.e2e.ts`,
`tests/oidc.e2e.ts`.

### Verification
Tests pass, including a real loopback test OIDC provider
(`tests/oidc-provider.ts`) — not a mocked identity check.

### Remaining Risks
Real SITHS/HSA connectivity, an approved clinic access matrix, and
independent security review remain open — see
[docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md) "Migration And
Pilot Gates".

---

## 2026-09-19 — Add medication reconciliation and owned laboratory result review

### Type
Feature

### Summary
Added the `eir.medications`/`eir.laboratories` providers: documented
medication use, versioned reconciliation, local lab orders, source-labelled
manual results, owned review, and correction-triggered reopening. See
[docs/MEDICATIONS-AND-RESULTS.md](../MEDICATIONS-AND-RESULTS.md).

### Reason
Extends the clinical record to two high-risk-tier domains (CLAUDE.md §60)
while deliberately not claiming prescribing or laboratory connectivity.

### Affected Areas
Backend, Database, API, Clinical workflow, Interoperability

### Files Changed
`plugins/medications.ts`, `plugins/laboratories.ts`,
`packages/medications.ts`, `packages/laboratories.ts`.

### Database Changes
New `medicationStatement`/review/`labOrder`/`labReport` entity kinds; no
schema migration (generic entity model).

### API Changes
New `/patients/:id/medications`, `/medications/:id`,
`/patients/:id/medication-reviews`, `/patients/:id/lab-orders`,
`/lab-orders/:id/receive`, `/lab-orders/:id/review`,
`/lab-orders/:id/cancel`.

### Clinical Safety Impact
Generic task completion cannot bypass required report review; critical
reports require explicit separate acknowledgement; corrections reopen
review even after completion.

### Interoperability Impact
FHIR export extended with `MedicationStatement`, `ServiceRequest`,
`DiagnosticReport`.

### Testing
`tests/medication-results.test.ts`, `tests/medication-results.e2e.ts`.

### Verification
Tests pass, including transaction-rollback-on-audit-failure and
patient/proxy visibility exclusion.

### Remaining Risks
No coded medication/lab catalogue, no prescribing workflow — see
[../features/medication-reconciliation.md](../features/medication-reconciliation.md).

---

## 2026-09-19 — Build modular care-team scheduling, inbox and recoverable drafts

### Type
Feature

### Summary
Added daily booking/check-in, the shared assigned task inbox with explicit
handover, encounter-linked appointment completion, and server-autosaved
note drafts. See [docs/CARE-TEAM.md](../CARE-TEAM.md).

### Reason
Closes the gap between "an encounter can be documented" and "a clinic's
actual daily workflow (scheduling, handover, draft recovery) is supported."

### Affected Areas
Backend, Database, API, Frontend, Clinical workflow

### Files Changed
`plugins/care-team.ts`, `packages/care-team.ts`, `apps/web/care-team.js`,
`apps/web/draft-editor.js`.

### API Changes
New `GET /care-team`, `POST /patients/:id/appointments`,
`POST /appointments/:id/:action`; note creation gains client-supplied
`clientId` idempotency.

### Clinical Safety Impact
Only the booked clinician may start an appointment; closing the linked
encounter completes the appointment atomically.

### Testing
`tests/care-team.test.ts`, `tests/care-team.e2e.ts`.

### Verification
Tests pass, including DST-boundary scheduling and overlap-detection cases.

### Remaining Risks
Single-process SQLite reference deployment; no availability
calendars/rooms/recurrence — see
[../features/care-team-scheduling.md](../features/care-team-scheduling.md).

---

## 2026-09-19 — Add branded EHR social preview and crawler-ready metadata

### Type
UX / Infrastructure

### Summary
Added static Open Graph/X-card metadata and a generated preview image for
the public demo and guide pages. See [docs/SHARING.md](../SHARING.md).

### Reason
Makes shared links to the public demo render correctly on social platforms
without requiring JavaScript or an account.

### Affected Areas
Frontend, DevOps

### Files Changed
`apps/web/social/eir-journal-v1.png`, `scripts/generate-social.ts`.

### Privacy Impact
The preview image is generated from a fresh fictional workspace, never a
real user's browser or patient data.

### Testing
`tests/social.e2e.ts` (crawler-user-agent checks, no JavaScript).

### Verification
Tests pass; verified against Facebook/LinkedIn/X crawler user agents.

### Remaining Risks
None recorded beyond ordinary link-preview cache staleness after future
image changes.

---

## 2026-09-19 — Build realistic clinical demo and official Swedish diagnosis lookup

### Type
Feature

### Summary
Added the official ICD-10-SE terminology importer/provider and a more
realistic synthetic clinical demo dataset. See
[docs/TERMINOLOGY.md](../TERMINOLOGY.md).

### Reason
Replaces placeholder diagnosis text with real, versioned, rights-respecting
Swedish coding, required before any diagnosis-recording workflow could be
called realistic.

### Affected Areas
Backend, Clinical workflow, Interoperability

### Files Changed
`plugins/terminology-icd-se.ts`, `packages/icd.ts`, `scripts/import-icd.ts`.

### Clinical Safety Impact
Server — not client — determines canonical code/label/version; historical
records are never silently recoded on a release change.

### Interoperability Impact
FHIR `Condition` export gains ICD-10-SE coding with release metadata.

### Testing
`tests/terminology.test.ts`.

### Verification
Checksum-pinned source import verified in CI and Docker build.

### Remaining Risks
Terminology lookup only — not a complete coding-rule engine (see
[../clinical/diagnoses.md](../clinical/diagnoses.md)).

---

## 2026-09-18 — Document public hosting and verify deployed demo workflows

### Type
Infrastructure / Documentation

### Summary
Documented and verified the Cloud Run + Firebase Hosting public-demo
topology, cost controls, and deployment order. See
[docs/HOSTING.md](../HOSTING.md).

### Reason
Made the actual (limited, disposable, synthetic-only) hosting posture
explicit and verifiable rather than assumed.

### Affected Areas
DevOps, Documentation

### Files Changed
`Dockerfile`, `firebase.json`, `scripts/deploy-hosting.ts`,
`scripts/prepare-hosting.ts`, `scripts/smoke-public.ts`, `docs/HOSTING.md`.

### Testing
`npm run smoke:public`, `EIR_DEMO_TEST_URL=... npm run test:e2e`.

### Verification
Deployed demo workflow verified against the live public URL.

### Remaining Risks
No guaranteed zero-cost ceiling; no exclusive EU data-residency claim — see
[docs/HOSTING.md](../HOSTING.md).

---

## 2026-09-18 — Build Sweden-first modular EHR and isolated public demo

### Type
Feature

### Summary
Initial working release: the plugin runtime, SQLite storage, local
identity, clinical record model (encounters/notes/observations/conditions/
allergies/tasks), AI proposal/review, FHIR R4 export, and the disposable
public demo entry point. See [docs/ARCHITECTURE.md](../ARCHITECTURE.md) and
[docs/PROJECT-STATUS.md](../PROJECT-STATUS.md).

### Reason
Establishes the executable foundation (M0 in [docs/PLAN.md](../PLAN.md))
that every subsequent milestone builds on.

### Affected Areas
Architecture, Backend, Database, Frontend, API, Interoperability, AI

### Files Changed
`packages/runtime.ts`, `packages/contracts.ts`, `plugins/storage-sqlite.ts`,
`plugins/clinical.ts`, `plugins/identity-local.ts`, `plugins/access.ts`,
`plugins/ai-extractive.ts`, `plugins/ai-review.ts`, `plugins/fhir-r4.ts`,
`apps/server.ts`, `apps/public-server.ts`, `apps/web/*`.

### Database Changes
Initial SQLite schema (`entities`, `versions`, `grants`, `restrictions`,
`sessions`, `audit`).

### API Changes
Initial `/api` surface (see [../api/README.md](../api/README.md)).

### Testing
`tests/clinical.test.ts`, `tests/workflow.e2e.ts`, `tests/public.e2e.ts`,
`tests/public-demo.test.ts`.

### Verification
Tests pass; public demo verified end-to-end.

### Remaining Risks
Every subsequent CHANGELOG entry above addresses a gap present at this
starting point (identity, persistence, lab integration, follow-up,
deterioration, coordination) — see [docs/PLAN.md](../PLAN.md) for the full
forward plan.
