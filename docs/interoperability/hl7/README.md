# HL7

Primary source: [.claude/agents/hl7.md](../../../.claude/agents/hl7.md),
[docs/FHIR.md](../../FHIR.md), [docs/INTEGRATIONS.md](../../INTEGRATIONS.md).

## Current state: not implemented

**There is no HL7 v2, v3, or CDA messaging integration anywhere in this
repository.** Before writing any HL7-related code, comment, or
documentation claim, read this page again — it is easy to conflate Eir's
own `eir.lab.v1` JSON protocol with HL7 because both move lab
orders/results, but they are unrelated:

| What exists | What it is NOT |
| --- | --- |
| `eir.lab.v1` (`plugins/integrations.ts`, `plugins/lab-transport-http.ts`) | Not HL7 v2 ORU/ORM, not a FHIR implementation guide, not an Inera contract |
| `eir.notification.v1` (`plugins/notification-http.ts`) | Not a national paging standard |
| FHIR R4 export (`plugins/fhir-r4.ts`) | Not HL7 v2; FHIR is HL7's *other* standard, and even there only an export projection exists — see [../fhir/README.md](../fhir/README.md) |

## [messages.md](messages.md), [ack-nack.md](ack-nack.md), [processing.md](processing.md), [error-handling.md](error-handling.md)

These four files exist per the required documentation structure but have no
content to document, because the corresponding capability is not built.
Each states the gap explicitly rather than describing a hypothetical design
as if implemented, per CLAUDE.md §106 ("No Fake Implementation") and §7
("Never Guess" — never invent HL7 mappings).

## If HL7 v2 integration is ever requested

This would be new work requiring: a message-parsing/validation layer,
message-ID tracking, ACK/NACK semantics, duplicate-message detection,
retry/ordering handling, processing-state tracking, source-system
identification, correlation IDs, and audit — see CLAUDE.md §27 for the full
requirement list. It would not reuse `eir.lab.v1`'s schema; it would need
its own contract, adapter, and conformance tests, following the same
durable-queue/lease/retry pattern already proven in
`plugins/integrations.ts` for the existing lab protocol (see
[../integrations/README.md](../integrations/README.md)).
