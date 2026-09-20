# FHIR: Profiles / Implementation-Guide Conformance

Primary source: [docs/FHIR.md](../../FHIR.md) ("Not Implemented Or Not Proven").

## Current state: no profile conformance is asserted

- No declared conformance to Swedish implementation guides, NLL profiles,
  IPS, EHDS exchange formats, or any contracted laboratory's profiles.
- No `CapabilityStatement` endpoint.
- No full HL7 validator/terminology-server report is part of CI. Tests
  verify selected mappings, visibility, source preservation, and workflow
  behavior — not complete schema/invariant/profile/terminology conformance.
- Identifier namespaces for Swedish national identifiers follow
  [HL7 Sweden's SEBasePatient](https://hl7.se/fhir/ig/base/1.0.0/StructureDefinition-SEBasePatient.html)
  base guide, without asserting full profile conformance to it.

## What a real profile-conformance claim would require

Choose one real receiving system; pin its FHIR version, implementation-guide
package, terminology releases, and supported interactions. Add the official
HL7 validator to CI with reproducible offline package inputs, representative
exports, and negative test cases. Resolve diagnostics and validate reference
resolution before claiming that profile. This work has not started. See
[docs/FHIR.md](../../FHIR.md) "Next Interoperability Milestone".
