---
name: fhir-review
description: Verify a FHIR-related change or claim in Eir EHR against what plugins/fhir-r4.ts and docs/FHIR.md actually implement. Use before changing FHIR export mappings or before any statement about FHIR capability.
---

# FHIR Review

Re-read [docs/FHIR.md](../../../docs/FHIR.md) and `plugins/fhir-r4.ts` before making or evaluating any FHIR claim — this list changes, and memory of it goes stale.

## Verify against the real implementation

- **Implemented**: one-directional export only, `GET /api/patients/:id/export/fhir`, returning a `Bundle` (`collection`). Patient, Encounter, Observation, Condition, AllergyIntolerance, DocumentReference, MedicationStatement, ServiceRequest, DiagnosticReport, Task.
- **Not implemented — flag any claim to the contrary**: general REST server (per-resource CRUD/search, `$validate`, `_history`, subscriptions, `CapabilityStatement`), import/bidirectional sync, SMART App Launch, any national/IG/NLL/IPS/EHDS conformance, full HL7-validator CI coverage, Bundle Composition/signature/Provenance/AuditEvent export, FHIR RiskAssessment.

## For a new/changed mapping

- Does it preserve `lastUpdated`, a version ID, and the existing `urn:uuid` full-URL + internal-reference scheme used by every other resource in the same Bundle?
- Is visibility actor-aware (clinician export may include drafts/internal tasks; patient/proxy export follows the stricter path)?
- Does it correctly show only the current/latest record where the domain has superseded versions (e.g. lab reports)?
- Is the Eir domain model kept separate from the FHIR shape (no FHIR types leaking back into `packages/contracts.ts`)?
- Are `sam*` (Eir Samverkan) entities and deterioration monitoring records still excluded, unless this change is deliberately and documentedly adding them?

## Before writing any "FHIR compliant" or "supports FHIR X" statement

State exactly which resource(s), which interaction (read-only export vs. REST), and whether a real FHIR validator or IG package was actually run against the output. If none was run, say so — do not imply conformance from mapping existence alone.

Update `docs/FHIR.md`'s tables in the same change if the mapped-resource list changed.
