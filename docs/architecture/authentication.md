# Architecture: Authentication

Primary source: [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md). Full
documentation: [../security/authentication.md](../security/authentication.md).

## Identity plugins

| Plugin | Purpose |
| --- | --- |
| `plugins/identity-local.ts` | Legacy local development identity (used by `eir.config.json`) |
| `plugins/identity-staff-local.ts` | Local synthetic staff identity for the public demo / strict-policy profiles (`localDevelopmentOnly` flag) |
| `plugins/identity-oidc.ts` | Real OIDC (`openid-client`) adapter for a clinic deployment |

All three implement the `Identity` interface (`packages/contracts.ts`):
`authenticate(token)`, optional `issue`/`revoke`/`select`, and an optional
`browser` sub-contract (`begin()`/`callback()`) used by the OIDC login flow.

## OIDC adapter behavior

Validates issuer, audience, JWKS signature, expiration, nonce, state, PKCE,
authentication time, and an `acr` assurance allowlist. Browser-bound,
five-minute, single-use login transactions prevent replay. Provider tokens
are discarded; a separate opaque EHR session token is hashed at rest and
delivered via an HttpOnly, `SameSite=Strict` cookie (Secure/`__Host-` naming
on HTTPS). Application tokens never enter redirect URLs or browser storage.
Maximum session lifetime is 8 hours; inactivity expiry defaults to 15 minutes
and is configurable up to 30.

## Architectural boundary

Authentication only resolves *who* is calling; it never determines *what*
they may do. That is `Access`/`Workforce`'s job — see
[authorization.md](authorization.md). Client-supplied actor/role/tenant
headers are always ignored server-side (CLAUDE.md §15).
