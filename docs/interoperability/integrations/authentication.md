# Integration Authentication

Primary source: [docs/INTEGRATIONS.md](../../INTEGRATIONS.md).

## Machine credentials are narrowly scoped

Machine credentials (used for lab result intake) authorize **only** result
intake and receipt lookup for their configured connector/tenant/unit. They
cannot authenticate at `/api/session`, read charts, sign notes, prescribe,
manage staff, or execute AI. Writes are attributed to `connector:<id>` with
audit role `integration` — no clinician identity is borrowed, and no
machine session is created in the same session store as staff sessions.
`packages/integration-auth.ts` implements this scoping.

## Credential generation and rotation

Outbound and inbound tokens per connector must be **different**,
independently generated random base64url tokens of at least 32 bytes
(43–256 permitted characters), injected through protected environment
variables — never JSON, source control, URLs, or logs. Rotation requires
coordinating both parties; overlapping-key grace periods are not
implemented.

## `integration.manage` is a separate administrative permission

It is not automatically granted to existing workforce assignments on
upgrade and must be assigned deliberately through the ordinary workforce
change workflow. Holding `integration.manage` allows inspecting connector
metadata, pausing/enabling connections, and retrying failed messages with a
reason — it does **not** allow editing envelopes, switching patients,
rerouting orders, forcing clinical application, or replaying already-
applied/acknowledged messages.

## Transport requirements

Remote endpoints require HTTPS with normal Node certificate verification.
URL credentials, queries, fragments, and redirects in the endpoint
configuration are rejected. Plain HTTP is accepted only under an explicit
`localDevelopmentOnly` flag bound to literal loopback addresses.
