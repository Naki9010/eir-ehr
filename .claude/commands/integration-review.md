---
description: Focused review of external-integration changes in Eir EHR — the lab pipeline, FHIR export, and any claimed HL7 work.
argument-hint: [optional connector or path]
---

Run an integration review of $ARGUMENTS (default: current diff against `main`) using the **integration**, **fhir**, **hl7**, **security**, and **clinical-safety** agents (CLAUDE.md §92).

Check specifically:

- **Contracts**: does the change match the existing `eir.lab.v1` wire schema in `packages/integrations.ts`, or does it introduce an incompatible parallel shape?
- **Authentication/authorization**: does a machine/connector credential stay scoped to result intake/receipt lookup for its own connector/tenant/unit only?
- **Validation**: is every inbound envelope validated against the strict `ResultMessage`/`OrderMessage` schema, with syntactically invalid input rejected without a receipt (`422`)?
- **Idempotency**: is `messageId` + `payloadHash` still the identity; does a reused ID with changed content correctly `409` rather than overwrite?
- **Duplicates / out-of-order**: does reprocessing the same message twice avoid a duplicate clinical action? Does a correction (`supersedesMessageId`) chain correctly?
- **Retry**: does a new failure mode fit the existing `pending → sending → acknowledged | rejected | retry | quarantined` states with bounded exponential backoff?
- **Reconciliation**: is an unmatched/ambiguous envelope durably quarantined for operator review rather than silently dropped or fuzzy-matched?
- **Audit**: is every accept/reject/quarantine/retry decision audited?
- **Failure recovery**: does the change account for restart-after-partial-acceptance and backup/restore replay risk (docs/INTEGRATIONS.md "Recovery And Limitations")?
- **HL7/FHIR claims**: does anything in the change or its description imply HL7 or FHIR REST-server capability that isn't actually implemented? (See the hl7 and fhir agents — `eir.lab.v1` is explicitly not HL7.)

Report using CLAUDE.md §85 format. Do not modify code unless explicitly asked.
