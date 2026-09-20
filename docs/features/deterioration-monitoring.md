# Deterioration Monitoring

Primary source: [docs/DETERIORATION.md](../DETERIORATION.md).

## Purpose

Optional, per-unit-activated vital-sign/trend monitoring with a clinician-
response workflow — explicitly not a validated mortality predictor.

## User Workflow

Authorized clinician enrolls an open encounter with a reason → worker
builds an encounter-scoped snapshot and evaluates it → `alert` /
`no-trigger` / `insufficient-data` / `unavailable` → an alert creates one
open alert + an urgent owned task → owner acknowledges, reassesses, and
explicitly resolves against the current assessment ID.

## Architecture

`eir.modules`/`Modules` (activation), `eir.risk.vitals`/`eir.risk.http`
(`RiskEngine`, exactly one selected), `eir.deterioration`/`Deterioration`
(lifecycle), existing task/follow-up services for ownership and
notification. See [../architecture/system-overview.md](../architecture/system-overview.md).

## Data Model

Monitoring/assessment/alert/response-event entities, private (not chart-
`patientId`-scoped in the general sense) and excluded from patient/proxy
views and FHIR export.

## API

`GET /api/modules`, `POST /api/modules/:id`,
`GET /api/deterioration?after=...`, `POST /api/patients/:id/monitoring`,
`POST /api/monitoring/:id/evaluate`, `POST /api/monitoring/:id/stop`,
`POST /api/deterioration-alerts/:id/respond`.

## Authorization

`modules.manage` to activate per unit (reason + expected revision
required); ordinary chart/task permissions for the clinical workflow.

## Security

Exactly one risk engine per composition; the external HTTP adapter rejects
plain HTTP outside explicit loopback development configuration.

## Privacy

The external-model payload omits direct patient identifiers but is **not**
anonymized — source references, timestamps, and lab text remain sensitive.

## Clinical Safety

An alert never auto-resolves from a lower value, a corrected observation,
missing data, or a model outage — only an explicit `resolve` against the
current assessment ID. `no-trigger` means only that the configured rules
didn't fire — never "low risk." See
[.claude/rules/clinical-safety.md](../../.claude/rules/clinical-safety.md)
"The deterioration module's specific safety rules".

## Audit

Automatic evaluation writes use an integration audit principal, never
recorded as a clinician decision; every activation change requires a
reason.

## Interoperability

`eir.risk.v1` — not CDS Hooks, not a FHIR prediction API. Linked tasks use
the existing FHIR Task projection; no FHIR `RiskAssessment`.

## Error Handling

Input changes mid-inference discard the result; the committing transaction
rechecks enrollment, activation revision, owner authorization, and source
versions.

## Concurrency

PostgreSQL version checks prevent duplicate alert/task commits across
worker replicas (though duplicate *inference calls* across replicas remain
possible — an explicit, documented residual behavior).

## Testing

`tests/deterioration.test.ts`, `tests/deterioration.e2e.ts`,
`tests/risk-http.test.ts`.

## Known Limitations

Bundled rules are development fixtures, explicitly "not NEWS2 and not
CHARTwatch"; clinical activation is blocked in the clinic example profile.

## Future Improvements

Local retrospective/prospective evaluation, calibration, subgroup analysis,
missed-deterioration/false-alert review, model change control — see
[docs/DETERIORATION.md](../DETERIORATION.md) "Before clinical use".
