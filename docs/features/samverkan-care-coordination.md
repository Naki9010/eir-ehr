# Eir Samverkan (Care Coordination)

Primary source: [docs/SAMVERKAN.md](../SAMVERKAN.md).

## Purpose

Shared, consented cross-unit cases (primary care / hospital / municipality)
within one tenant, with structured SIP (samordnad individuell plan),
attachments, and payment estimation — Eir's own implementation, not SAMSA
and not connected to SAMSA.

## User Workflow

Enable the module per unit (Moduler) → create a case with scoped, expiring
consent → send messages (care request, admission, discharge-ready,
discharge, etc.) between units → build a SIP with participants, goals, and
follow-up → finalize and, on discharge, acknowledge outstanding messages
before closing the case.

## Architecture

`coordinationDirectory`, `coordination`, `sipPlans`, `coordinationPayment`,
`coordinationDocuments`, `coordinationNotifications` — each independently
replaceable (`packages/coordination.ts`). `apps/web/coordination-
workspace.js`.

## Data Model

Private `sam*`-prefixed entity kinds — no chart `patientId`, excluded from
chart views, generic history/change feeds, and FHIR projections.

## API

`GET/POST /coordination`, `/coordination/cases/:id`, `/consent`, `/action`,
`/messages`, `/sip`, `/attachments`, `/pdf`. Full table:
[docs/SAMVERKAN.md](../SAMVERKAN.md) "API".

## Authorization

`coordination.read`/`write`/`manage`/`export`/`billing`/`discharge` —
distinct permissions per capability; writes additionally require unit
activation and contributing membership.

## Security

Attachments are quarantined by default; only synthetic profiles permit
immediate PDF download. PDF parsing runs off the HTTP event loop with a
bounded heap and a 2-second deadline — not an OS-level sandbox.

## Privacy

Messages are visible only to sending/receiving units; consent withdrawal
prevents new sharing without deleting history.

## Clinical Safety

Discharge requires a fixed primary-care contact and confirmed outpatient
availability — application guards, not a clinical decision engine.

## Audit

Every message, consent change, and process transition is versioned and
audited; retraction is allowed only in reverse transition order and
retains history.

## Interoperability

No SAMSA import/export, no FHIR CarePlan/Communication/Consent mapping —
authenticated Eir JSON APIs only.

## Error Handling

Case reads are bounded to 1,000 records per kind; exceeding that returns a
conflict rather than silently dropping older data.

## Concurrency

Message/receipt/notification/case-transition writes commit together;
conflicting edits return `409`.

## Testing

`tests/coordination.test.ts`, `tests/coordination.e2e.ts`.

## Known Limitations

Single-tenant demonstration, not regional federation; SEK 5,000/day payment
rate is fictional, not an actual Swedish/VGR rate; statutory exception
pathways (compulsory care, inability-to-consent proxies) are not
implemented.

## Future Improvements

SITHS/HSA onboarding, verified organizations, federation agreements,
approved payment rates, production document scanning — see
[docs/SAMVERKAN.md](../SAMVERKAN.md) "Next Release Gates".
