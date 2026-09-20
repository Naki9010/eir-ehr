# FHIR: Exported Resources

Primary source: [docs/FHIR.md](../../FHIR.md).

| Eir record | Exported resource | Current representation |
| --- | --- | --- |
| Patient | Patient | Name, date of birth, identifier; local IDs are tenant-scoped |
| Encounter | Encounter | Ambulatory encounter, subject, reason, period |
| Vitals | Observation | Selected LOINC codes, UCUM quantity units |
| Diagnosis | Condition | ICD-10-SE coding + release metadata, clinical/error status |
| Allergy | AllergyIntolerance | Text substance/reaction, status, criticality |
| Note | DocumentReference | Base64 UTF-8 text, author, encounter, amendment relationship |
| Medication use | MedicationStatement | Documented use, free-text dose/provenance — **not a prescription** |
| Laboratory order | ServiceRequest | Text test/specimen/question, order lifecycle |
| Laboratory report | DiagnosticReport | **Current report only** with contained Observations, source identifiers, flags |
| Follow-up task | Task | Owner identifier, status, priority, date or exact deadline |

Superseded lab reports are retained in Eir history but omitted from the
current export. Laboratory values currently use `valueString`, not a fully
coded quantitative model. Visibility follows the authenticated actor's
permitted view — a clinician export may include draft notes and internal
tasks permitted in their chart; a citizen/proxy export follows the more
restrictive visibility path where that identity mode is configured (not yet
available in the clinic profile).

Entries use `urn:uuid` full URLs, internal contained-resource references,
resource version IDs, and export-time metadata. Export authorization and
chart access are checked *inside* the storage transaction, and the export
itself is audited.
