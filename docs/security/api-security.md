# API Security

Primary source: [docs/API.md](../API.md).

## Transport and request constraints

Every `/api` endpoint requires `Authorization: Bearer <session>` issued by
the configured identity service; client-supplied actor/role/tenant headers
are always ignored. Request bodies are limited to 128 KiB. Rate limits
(`@fastify/rate-limit`) and same-origin checks apply. Cookie-authenticated
writes (the OIDC clinic profile) additionally require an exact configured
`Origin` match — the practical CSRF defense for that profile.

## Error shape

Errors are `{error, fields?}` with predictable statuses: `401` invalid/
expired session, `403` authorization, `404` missing record, `409` revision/
lifecycle conflict, `422` validation. See [../api/errors.md](../api/errors.md).
Server request logging never includes secrets or record content
(docs/API.md).

## Input validation

Centralized typed schemas (mostly Zod) in `packages/*.ts` validate every
request payload before domain logic runs — e.g. vital-sign code/unit
pairing and bounds, diagnosis code/version checks, lab-report field bounds
(30 analytes/report, offset-datetime ordering). These are input-integrity
checks, not diagnostic interpretation (docs/API.md).

## Public-demo-specific hardening

The public demo additionally caps workspaces (20 per process), API requests
(400), writes (60), and cumulative request-body bytes (256 KiB) per
workspace, with individual requests capped at 32 KiB. It deliberately does
not trust arbitrary forwarded IP headers for rate-limiting. See
[docs/HOSTING.md](../HOSTING.md) "Cost Controls" — this is capacity/abuse
protection for a disposable demo, not a general DDoS protection service.

## Not yet implemented / not verified here

No CSP regression test suite, no documented security-header baseline audit,
no independent penetration test. See [dependency-security.md](dependency-security.md)
for the one automated check that does exist (`npm audit` in CI).
