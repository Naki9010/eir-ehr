# FHIR: Validation

Primary source: [docs/FHIR.md](../../FHIR.md).

## What is verified today

Tests exercising the FHIR export path: `tests/medication-results.test.ts`,
`tests/terminology.test.ts`, `tests/security.test.ts`,
`tests/integration.test.ts`, and `scripts/smoke-public.ts`. These verify
selected mappings, visibility rules (e.g. drafts/internal records excluded
from a citizen/proxy export), and source-data preservation through export.

## What is not verified

There is no official HL7 FHIR validator run in CI, no reproducible offline
implementation-guide package validated against exported examples, and no
`$validate` operation available on the API (there is no general FHIR REST
server at all — see [../hl7/README.md](../hl7/README.md) for the parallel
statement about HL7 v2). Repository projection tests are engineering
evidence of mapping correctness for the cases they cover; they are not a
substitute for an official validator and target implementation-guide test
suite (docs/REGULATORY-ALIGNMENT.md).

## Before any conformance claim

Do not claim "FHIR compliant" or "FHIR validated" without running the
official validator against a pinned implementation-guide release and
recording the result. See CLAUDE.md §112 for the required precision in
verification language.
