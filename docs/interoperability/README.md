# Interoperability Documentation

Three distinct areas, at three very different maturity levels:

| Area | Status |
| --- | --- |
| [fhir/](fhir/README.md) | Implemented: an authenticated FHIR R4 **export projection**, not a full FHIR server |
| [hl7/](hl7/README.md) | **Not implemented.** No HL7 v2/v3 messaging exists in this repository |
| [integrations/](integrations/README.md) | Implemented: a durable, Eir-specific JSON protocol (`eir.lab.v1`, `eir.notification.v1`) for lab and notification transport — not HL7, not a national service connection |

Primary sources: [docs/FHIR.md](../FHIR.md), [docs/INTEGRATIONS.md](../INTEGRATIONS.md),
[docs/SWEDISH-INTEGRATIONS.md](../SWEDISH-INTEGRATIONS.md). Before touching
anything in this folder, read [.claude/agents/fhir.md](../../.claude/agents/fhir.md),
[.claude/agents/hl7.md](../../.claude/agents/hl7.md), and
[.claude/agents/integration.md](../../.claude/agents/integration.md).

## The single most important fact in this folder

**No Swedish national service (SITHS, 1177, NPÖ, Nationella
läkemedelslistan, EHDS exchange, Webcert, any regional lab/referral system)
is connected.** Every capability described here is either a local/protocol-
test implementation or an internal export projection. See
[docs/SWEDISH-INTEGRATIONS.md](../SWEDISH-INTEGRATIONS.md) for exactly who
owns each of those integrations and what onboarding would require.
