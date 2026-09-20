# FHIR

Primary source: [docs/FHIR.md](../../FHIR.md) (reviewed 2026-09-19 against
`plugins/fhir-r4.ts`). Full detail lives there; this folder breaks it into
navigable topics.

## Contents

- [resources.md](resources.md) — exported resource types and their current representation
- [profiles.md](profiles.md) — implementation-guide/profile conformance (none asserted)
- [mappings.md](mappings.md) — Eir record kind → FHIR resource mapping
- [validation.md](validation.md) — what validation exists today

## One-line summary

`GET /api/patients/:id/export/fhir` returns an authenticated, authorized,
audited FHIR R4-style JSON `Bundle` (type `collection`) — a projection of
the current authorized record, not a general FHIR REST server, and not a
declared conformance to any Swedish implementation guide.
