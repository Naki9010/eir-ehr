# Idempotency

See [../interoperability/integrations/retry-and-idempotency.md](../interoperability/integrations/retry-and-idempotency.md)
for the detailed integration-specific treatment; this page is the general
reliability statement.

## The pattern, generalized

Every outbound message carries a stable `messageId` (delivered as
`Idempotency-Key`) and a `X-Eir-Payload-Sha256` payload hash. The receiver
is contractually required to durably deduplicate and persist its response
**before** acknowledging — Eir treats the network as at-least-once and
never claims exactly-once delivery across it (CLAUDE.md §28).

## Client-side creation idempotency

Several creation endpoints accept a client-supplied `clientId` (UUID) to
make retried creation safe after an uncertain network response: note
creation, medication/reconciliation creation, lab-order creation. Reusing
the same `clientId` with **identical** input returns the existing record;
reusing it with **different** input returns `409` rather than silently
creating a duplicate or silently applying the new data to the old record.

## Where this matters most for patient safety

Duplicate delivery of a lab result or a follow-up notification must never
cause a duplicate clinical action (CLAUDE.md §27's HL7 framing applies
equally to Eir's own protocols) — this is why "one clinical application per
connector/message identity" is enforced at the storage layer
(`packages/lab-application.ts`), not merely assumed from transport-layer
deduplication.
