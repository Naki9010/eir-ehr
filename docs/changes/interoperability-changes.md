# Interoperability Changes

Filtered view of [CHANGELOG.md](CHANGELOG.md) for FHIR/HL7/integration-
relevant entries.

| Date | Change | Detail |
| --- | --- | --- |
| 2026-09-19 | `eir.lab.v1` lab integration protocol | [CHANGELOG](CHANGELOG.md#2026-09-19--add-reliable-laboratory-integration-runtime-and-operator-workflows) |
| 2026-09-19 | `eir.notification.v1` (via follow-up), reused by coordination | [CHANGELOG](CHANGELOG.md#2026-09-19--add-clinical-follow-up-workflows-and-regulatory-evidence-brief) |
| 2026-09-19 | `eir.risk.v1` external-model adapter contract | [CHANGELOG](CHANGELOG.md#2026-09-19--add-optional-deterioration-monitoring-and-unit-level-module-controls) |
| 2026-09-19 | FHIR export extended: `MedicationStatement`, `ServiceRequest`, `DiagnosticReport` | [CHANGELOG](CHANGELOG.md#2026-09-19--add-medication-reconciliation-and-owned-laboratory-result-review) |
| 2026-09-19 | FHIR `Condition` gains ICD-10-SE coding + release metadata | [CHANGELOG](CHANGELOG.md#2026-09-19--build-realistic-clinical-demo-and-official-swedish-diagnosis-lookup) |
| 2026-09-18 | Initial FHIR R4 export projection (Patient, Encounter, Observation, Condition, AllergyIntolerance, DocumentReference) | [CHANGELOG](CHANGELOG.md#2026-09-18--build-sweden-first-modular-ehr-and-isolated-public-demo) |

**HL7 has never appeared in this log** because it has never been
implemented — see [../interoperability/hl7/README.md](../interoperability/hl7/README.md).
No entry above represents a connection to a real external system — see
[../interoperability/integrations/integration-catalog.md](../interoperability/integrations/integration-catalog.md)
for exact status per integration.
