# Interoperability Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md) §26–§28, §49. This file gives the fhir/hl7/integration agents the detailed shape.

## FHIR — capability inventory (re-verify against [docs/FHIR.md](../../docs/FHIR.md) before any claim)

**Implemented**: one-way export only, `GET /api/patients/:id/export/fhir` → a `Bundle` (type `collection`) via `plugins/fhir-r4.ts`. Mappings: Patient, Encounter, Observation (vitals), Condition (diagnoses), AllergyIntolerance, DocumentReference (notes), MedicationStatement, ServiceRequest (lab orders), DiagnosticReport (lab reports, latest per order only), Task (follow-up).

**Not implemented**: general FHIR REST server (no per-resource CRUD/search, `$validate`, `_history`, subscriptions, `CapabilityStatement`), FHIR import/bidirectional sync, SMART App Launch, any declared IG/NLL/IPS/EHDS conformance, full HL7-validator CI coverage, Bundle Composition/signature/Provenance/AuditEvent, FHIR RiskAssessment (deterioration stays as private records + Task projection).

Treat this as a living list — whenever you change `plugins/fhir-r4.ts`, update docs/FHIR.md's tables in the same change so the "implemented vs. not" split stays accurate.

## HL7 — there is none

No HL7 v2/v3 parsing, message types, ACK/NACK handling, or segment mapping exists anywhere in this repository. `eir.lab.v1` (the actual lab protocol) and `eir.notification.v1` are Eir's own JSON-over-HTTPS designs, explicitly documented as *not* HL7. Any request that says "HL7" almost always means the existing lab integration — confirm before writing anything HL7-specific, and never let generated code, comments, or docs imply HL7 support that isn't there.

## The lab integration protocol (`eir.lab.v1`) — the pattern any new integration should match

```
Clinician selects lab → order + envelope + task committed atomically
        │
        ▼
Leased worker sends outbound (Idempotency-Key: messageId, X-Eir-Payload-Sha256: hash)
        │
        ▼
Ack must match message/order/patient/hash → pending → sending → acknowledged | rejected | retry | quarantined
        │
        ▼
Lab posts result to POST /integrations/:connectorId/results (separate machine token, outside /api)
        │
        ▼
202 = durable receipt, NOT clinical application
        │
        ▼
Matching (connector scope, exact patient/order ID, order ack, restrictions, correction predecessor, active reviewer)
        │
        ▼
Atomic apply: report + order + task + versions + audit commit together
```

At-least-once delivery is assumed throughout; correctness comes from message-ID + payload-hash idempotency and version checks, never from a delivery guarantee. A reused message ID with different content is `409` plus an audit denial — never a silent overwrite. Corrections chain via `supersedesMessageId`, retaining every prior report in history.

## Change feed

`/api/patients/:id/changes` is a polling, at-least-once, cursor-based feed of immutable version rows — not a FHIR Subscription or a push webhook. Consumers must deduplicate by `(record.id, record.version)` and persist `nextCursor` even on an empty page (hidden draft/proposal versions may have been scanned).

## Before claiming any new interoperability capability

1. Identify the real receiving/sending system and its actual, specific contract — never infer one from general standards knowledge.
2. Pin the exact version/IG/protocol.
3. Add representative and negative test cases under `tests/`.
4. Update `docs/FHIR.md` or `docs/INTEGRATIONS.md`'s "implemented / not implemented" tables in the same change.
5. Only then update any user-facing claim.
