---
name: reliability-review
description: Review failure modes, retry, idempotency and recovery behavior for an Eir EHR worker, queue, or external-dependency path. Use for changes to the integration/follow-up/deterioration/coordination workers or any new async processing.
---

# Reliability Review

See `.claude/rules/reliability.md` for the full detail this skill draws from.

## Fast checklist

1. What happens on process crash mid-cycle — is partially-applied work left in a safe, resumable state?
2. What happens on duplicate delivery — is there a stable idempotency key (message ID + payload hash, or `(id, version)`) that prevents a duplicate clinical action?
3. What happens on a stale/abandoned lease — does a new lease properly fence off the old holder?
4. What happens on a downstream timeout or 5xx — does it retry with bounded exponential backoff and jitter, or fail immediately without a retry path?
5. Is there a visible operational signal for the new failure mode (quarantine count, oldest-pending-work age, failed-cycle counter) rather than only a log line?
6. Does the new worker/query stay bounded (page size, cursor, capped batch) rather than an unbounded scan?
7. If this touches durable queue state, does backup/restore replay risk get considered (docs/INTEGRATIONS.md "Recovery And Limitations")?
8. Is a scale-to-zero / request-billed deployment being assumed for something that actually needs continuous processing? Flag it if so.

## Reporting

For each finding, describe the concrete failure scenario (e.g. "worker crashes after sending the outbound request but before recording `sending` state — a restart will resend with the same `Idempotency-Key`, which is safe, but confirm the receiver dedupes on it") rather than a generic "add error handling."
