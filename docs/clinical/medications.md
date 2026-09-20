# Medications

Primary source: [docs/MEDICATIONS-AND-RESULTS.md](../MEDICATIONS-AND-RESULTS.md).

## What this is (and is not)

`eir.medications` documents **reported medication use**, not prescribing or
dispensing. It records name, free-text dose instruction (`dosageText`,
explicitly nullable for "unknown"), indication, `source` (`patient` |
`record` | `caregiver`), and `status` (`active` | `on-hold` | `stopped` |
`entered-in-error`). There is no coded medication catalogue, dose validator,
interaction checker, or prescribing/dispensing workflow.

## Reconciliation

A reconciliation ("avstämning") is an exact, versioned snapshot of **all**
medication and allergy entity versions at that moment (`review.snapshot`:
sorted `id@version` strings, including corrected entries). Any subsequent
addition or correction makes that reconciliation stale. An empty medication
list is displayed as *unknown* until a clinician explicitly confirms "no
current medicines" (`noCurrentMedicines: true`). A `current: true` flag
means the recorded list was reviewed against the current snapshot — **not**
that every dose was clinically verified or that a medication-safety check
was performed.

## API

| Method | Route | Payload |
| --- | --- | --- |
| GET | `/patients/:id/medications` | `{items, snapshot, review, current}` |
| POST | `/patients/:id/medications` | `{clientId, name, dosageText, indication, source, sourceDetail, status}` |
| POST | `/medications/:id` | `{version, data: {..., reason}}` |
| POST | `/patients/:id/medication-reviews` | `{clientId, snapshot, source, note, confirmed: true, noCurrentMedicines}` |

`clientId` (UUID) makes creation retryable; reusing it with different input
returns `409`. A voided statement cannot be restored — a new statement must
be created.

## FHIR

Exported as `MedicationStatement` — documented use and free-text
dose/provenance, explicitly **not** a prescription (`MedicationRequest`/
`MedicationDispense` are not emitted). See
[../interoperability/fhir/resources.md](../interoperability/fhir/resources.md).

## AI evidence

AI proposal evidence includes documented medication status/source/unknown
dosage; source changes invalidate pending proposals. AI cannot alter
medications — it can only propose draft note text a clinician separately
reviews and signs. See [../ai/safety.md](../ai/safety.md).
