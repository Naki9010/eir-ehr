# Retry and Idempotency

Primary source: [docs/INTEGRATIONS.md](../../INTEGRATIONS.md),
[docs/FOLLOW-UP.md](../../FOLLOW-UP.md). See also
[../../reliability/retry.md](../../reliability/retry.md) and
[../../reliability/idempotency.md](../../reliability/idempotency.md) for the
general reliability treatment; this page is the integration-specific detail.

## The shared pattern

Every outbound integration (lab orders, follow-up notifications,
coordination notifications) uses the same shape:

1. A durable outbox row is written in the **same transaction** as the
   domain action that created the need to send (e.g. placing a lab order).
2. A worker reserves due work with an **expiring lease**; new leases fence
   off workers that died mid-send.
3. Delivery carries a stable `messageId` (`Idempotency-Key` header) and a
   `X-Eir-Payload-Sha256` payload hash. Retries reuse the exact same message
   ID and payload — never a new one for the same logical attempt.
4. The receiver must durably deduplicate and persist its response **before**
   acknowledging. Eir treats network delivery as **at-least-once**; it never
   claims exactly-once delivery across a network boundary.
5. Exponential backoff with jitter, a one-hour cap, and a bounded attempt
   budget (lab: default 6 attempts). An exhausted budget leaves the message
   visibly failed, requiring an authorized, reasoned, versioned replay — not
   a silent drop.

## Inbound deduplication

Reusing an inbound message ID with **identical** content returns the
original receipt (true idempotent replay). Reusing an ID with **changed**
content returns `409` and appends a denial audit event — it never silently
overwrites the original.

## Route/destination changes mid-flight

If a follow-up notification's destination changes after a message was
queued, dispatch detects the changed route and cancels the stale envelope;
the next evaluation creates a fresh message with a **different** message ID
bound to the new route. Normal retries against an unchanged route always
keep the original message ID.
