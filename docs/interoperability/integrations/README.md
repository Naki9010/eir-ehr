# External Integrations

Primary source: [docs/INTEGRATIONS.md](../../INTEGRATIONS.md),
[docs/FOLLOW-UP.md](../../FOLLOW-UP.md), [docs/SAMVERKAN.md](../../SAMVERKAN.md)
("Notifications"), [docs/SWEDISH-INTEGRATIONS.md](../../SWEDISH-INTEGRATIONS.md).

## Contents

- [integration-catalog.md](integration-catalog.md) — every integration, its status, and protocol
- [authentication.md](authentication.md) — machine credentials and scoping
- [retry-and-idempotency.md](retry-and-idempotency.md) — the shared delivery pattern
- [reconciliation.md](reconciliation.md) — what happens when things go wrong

## Summary

Two working transport protocols exist, both Eir-specific JSON-over-HTTPS,
both explicitly **not** HL7 and **not** connected to any Swedish national
service today:

- `eir.lab.v1` — laboratory order dispatch and result intake
  (`plugins/integrations.ts`, `plugins/lab-transport-http.ts`).
- `eir.notification.v1` — generic staff-notification delivery, shared by the
  follow-up module and Eir Samverkan
  (`plugins/notification-http.ts`).

Default public/staging profiles ship these plugins with **no configured
external destinations and no background worker** — the protocol exists and
is tested against a local sandbox, but nothing is connected to a real
laboratory or notification gateway. See
[docs/SWEDISH-INTEGRATIONS.md](../../SWEDISH-INTEGRATIONS.md) for who
actually owns each real-world Swedish integration and what onboarding it
requires.
