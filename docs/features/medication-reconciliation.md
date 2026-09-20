# Medication Reconciliation

Primary source: [docs/MEDICATIONS-AND-RESULTS.md](../MEDICATIONS-AND-RESULTS.md).

## Purpose

Document reported medication use and produce a versioned, explicit
snapshot-based reconciliation of the current medication/allergy picture —
without ever silently asserting "no known medications."

## User Workflow

Record medication use (name, dose text or explicit unknown, indication,
source, status) → periodically reconcile against the current versioned
snapshot of all medication + allergy entities → confirm current, or
explicitly confirm "no current medicines."

## Architecture

`Medications` interface (`packages/contracts.ts`) implemented by
`plugins/medications.ts`. See [../clinical/medications.md](../clinical/medications.md).

## Data Model

`medicationStatement`-kind entities plus a `review` snapshot entity
(sorted `id@version` list). See [../database/entities.md](../database/entities.md).

## API

`GET/POST /patients/:id/medications`, `POST /medications/:id`,
`POST /patients/:id/medication-reviews`. Schemas in `packages/medications.ts`.

## Authorization

`medication.write` to record/update, `medication.reconcile` to reconcile.

## Security

Clinician-only; no patient/proxy write path exists for medications.

## Privacy

Clinician-only visibility in chart, history, change feed, and export —
patient/proxy release needs its own reviewed policy, not yet implemented
(docs/MEDICATIONS-AND-RESULTS.md "Visibility, AI And FHIR").

## Clinical Safety

An empty list displays as unknown until explicit confirmation. A
reconciliation becomes stale the instant any medication/allergy entity
changes. A voided statement cannot be restored — a new one must be created.
See [../clinical/medications.md](../clinical/medications.md).

## Audit

Every write and reconciliation is audited; report/review/task writes share
one transaction, with tested rollback on audit-write failure.

## Interoperability

Exported as FHIR `MedicationStatement` only — explicitly not a prescription
resource. See [../interoperability/fhir/resources.md](../interoperability/fhir/resources.md).

## Error Handling

`409` for a `clientId` reuse with changed input or a stale version; no
coded medication catalogue means no drug-specific validation errors exist
yet.

## Concurrency

Compare-and-set versions on every update/reconciliation; retryable creation
via `clientId`.

## Testing

`tests/medication-results.test.ts`, `tests/medication-results.e2e.ts`.

## Known Limitations

No coded medication catalogue, dose validator, interaction checker, or
prescribing/dispensing workflow. Not connected to Nationella
läkemedelslistan.

## Future Improvements

Tracked in [docs/PLAN.md](../PLAN.md) "Medicines" (M3): NLL integration,
prescription workflow, drug-knowledge provider, structured dose/route
validation.
