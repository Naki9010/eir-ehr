# Diagnoses

Primary source: [docs/TERMINOLOGY.md](../TERMINOLOGY.md), [docs/API.md](../API.md).

## Terminology source

`eir.terminology.icd-se` implements the `Terminology` contract (release
metadata, local code/text search, exact lookup) against the official
ICD-10-SE release valid from 2026-01-01, imported via
`npm run terminology:import` from
[E-hälsomyndigheten's classification downloads](https://samarbetsyta.ehalsomyndigheten.se/spaces/IR2/pages/451267009/Ladda%2Bner%2Bfiler%2Bf%C3%B6r%2Bklassifikationer),
with a pinned SHA-256 checksum recorded in `packages/icd.ts`. 38,631
codes/categories are derived with Swedish labels and search terms; generated
data lives outside the Apache-licensed repository in ignored `.terminology/`.

## Recording a condition

`POST /api/patients/:id/records/condition`:
`{code: {system, version?, code, display}, onset?}`. The server — not the
client — determines the canonical code, label, and coding-release version;
a client-supplied label is never trusted. Three-character categories that
still have subcodes cannot be saved directly. Unknown codes, unsupported
systems, and non-specific-category codes return `422`; a stale release
version returns `409`. **Historical conditions are never silently recoded**
when the terminology release changes.

## Correction

Like allergies and observations, diagnoses use `correct` (previous version
retained as `entered-in-error`, new record created) — never an in-place
edit.

## Explicit limitations

This is a terminology *lookup*, not an automated diagnosis or complete
coding-rule engine — it does not apply exclusion, combination, principal-
diagnosis, or sequencing rules. The clinical record stores an unranked
problem, not a coded billing claim. Manifestation codes and codes unsuitable
as a principal diagnosis are flagged in search results, but clinical coding
review remains necessary. See [../clinical/clinical-governance.md](clinical-governance.md)
for why coding-system choice itself is a clinical/organizational decision,
not an engineering one.

## FHIR

Exported as `Condition` with ICD-10-SE coding, release metadata, and
clinical/error status (see [../interoperability/fhir/resources.md](../interoperability/fhir/resources.md)).
