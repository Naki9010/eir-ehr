# Retry

Primary source: [docs/INTEGRATIONS.md](../INTEGRATIONS.md),
[docs/FOLLOW-UP.md](../FOLLOW-UP.md), [docs/SAMVERKAN.md](../SAMVERKAN.md).

## Shared backoff shape

Exponential backoff with jitter, a one-hour cap, and a bounded attempt
budget — lab integration defaults to 6 attempts (configurable
`maxAttempts`); coordination notifications default to 5 attempts with a
60-second claim and a ≤10-second HTTP timeout; follow-up notifications
default to a budget of 6.

## What retries, and what doesn't

Automatically retried: network faults, `429`/`5xx` responses, missing
predecessor acknowledgements. **Not** automatically retried: wrong
identity, conflicting corrections, patient restrictions, invalid
acknowledgements, or unavailable review ownership — these require human
intervention because blindly retrying them could apply the wrong clinical
action (docs/INTEGRATIONS.md "Recovery And Limitations").

## Exhausted retry is visible, not silent

An exhausted attempt budget leaves the message in a visibly failed state in
the relevant operations UI (integration workspace, follow-up Bevakning
view, coordination inbox). Recovery requires an authorized, **reasoned**,
**version-checked** replay — never an automatic retry-forever loop and
never a silent drop (see [../clinical/patient-safety.md](../clinical/patient-safety.md)
"received ≠ reviewed ≠ acted on").

## Replay does not reset identity

Audited replay resets the attempt *budget*, not the message's identity —
the same `messageId`/payload hash is reused unless the underlying route or
content actually changed (see [idempotency.md](idempotency.md)).
