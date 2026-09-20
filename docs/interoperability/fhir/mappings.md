# FHIR: Mappings

Primary source: `plugins/fhir-r4.ts`, [docs/FHIR.md](../../FHIR.md).

See [resources.md](resources.md) for the full Eir-record → FHIR-resource
table. This file records mapping-specific caveats not covered there:

- **Separation of domain model from FHIR representation.** The FHIR plugin
  reads the same authorized entity snapshots the rest of the API uses; it
  does not introduce a parallel FHIR-shaped database or a dual-write path.
  There is deliberately no openEHR/FHIR dual-write — Eir's clinical record
  is canonical locally, and FHIR is a versioned projection at the boundary
  (docs/ARCHITECTURE.md).
- **References** use Bundle UUID URNs and contained-resource references
  (e.g. a `DiagnosticReport`'s `Observation`s are contained, not separate
  Bundle entries).
- **Provenance**: resource version IDs and last-updated metadata are
  included; there is no separate `Provenance`/`AuditEvent` resource export,
  and no cryptographic Bundle signature.
- **Coding systems**: only ICD-10-SE (Condition) and a fixed set of LOINC
  codes + UCUM units (Observation) are mapped; laboratory analytes remain
  free-text (`valueString`), not coded to LOINC/NPU.
- **`eir.risk.v1`** (deterioration module) and **`eir.lab.v1`**/
  **`eir.notification.v1`** (integrations) are Eir-specific JSON protocols,
  not FHIR mappings — do not describe them as FHIR-based. Monitoring
  settings, assessments, and response events are private application
  records; there is no FHIR `RiskAssessment` implementation.
