# Patient Registration and Chart

Primary source: [docs/API.md](../API.md), [docs/ARCHITECTURE.md](../ARCHITECTURE.md).

## Purpose

Identify a patient, establish an initial care relationship, and provide the
authorized clinical record (chart) that every other clinical workflow is
built on.

## User Workflow

Register (name, birth date, identifier) → registering clinician receives a
30-day local care assignment → open the chart → open an encounter → capture
observations/conditions/allergies/notes/tasks against that encounter.

## Architecture

`Clinical.register`/`Clinical.chart` (`packages/contracts.ts`) implemented
by `plugins/clinical.ts`, backed by `Store` (`plugins/storage-sqlite.ts` /
`plugins/storage-postgres.ts`). See
[../architecture/system-overview.md](../architecture/system-overview.md).

## Data Model

Generic `Entity` (`kind='patient'`, `kind='encounter'`, etc.) — see
[../database/entities.md](../database/entities.md). Patient identifier
uniqueness enforced by a filtered unique index
(`identifier_unique`, see [../database/indexes.md](../database/indexes.md)).

## API

`POST /api/patients`, `GET /api/patients`, `GET /api/patients/:id/chart`,
`POST /api/patients/:id/records/:kind`, `POST /records/:id/:action`,
`GET /records/:id/history`, `GET /patients/:id/changes`. Full detail:
[../api/endpoints.md](../api/endpoints.md).

## Authorization

`patient.register` to create; `chart.read` to view; `record.write` to
mutate; `patient.protected` additionally required for protected patients.
See [../security/authorization.md](../security/authorization.md).

## Security

Server-side patient-ID authorization on every request; no trust of
client-supplied identity. See [../security/access-control.md](../security/access-control.md).

## Privacy

Visibility filtering (`packages/visibility.ts`) excludes drafts/proposals/
internal records from patient/proxy views. See
[../privacy/data-flows.md](../privacy/data-flows.md).

## Clinical Safety

At most one open encounter per patient; wrong-patient scenarios explicitly
tested. See [../clinical/patient-safety.md](../clinical/patient-safety.md).

## Audit

Registration, chart reads, and every record mutation generate audit events
(actor, action, patient, entity, outcome). See
[../architecture/audit.md](../architecture/audit.md).

## Interoperability

Chart entities are the source for the FHIR export projection — see
[../interoperability/fhir/resources.md](../interoperability/fhir/resources.md).

## Error Handling

`404` unknown patient, `403` unauthorized, `422` invalid identifier/payload,
`409` duplicate identifier or stale version. See [../api/errors.md](../api/errors.md).

## Concurrency

Every clinical entity uses expected-version optimistic concurrency. See
[../database/concurrency.md](../database/concurrency.md).

## Testing

`tests/clinical.test.ts`, `tests/postgres-clinical.test.ts`,
`tests/security.test.ts`, `tests/workflow.e2e.ts`.

## Known Limitations

No patient merge/unmerge, no local reserve-ID lifecycle, no verified
citizen/proxy onboarding. See [../clinical/patient-identity.md](../clinical/patient-identity.md).

## Future Improvements

Tracked as P1 backlog in [docs/PLAN.md](../PLAN.md) ("Patient identity
lifecycle"): local reserve ID, corrected birth dates, deceased/unknown
patient handling, merge/unmerge with evidence.
