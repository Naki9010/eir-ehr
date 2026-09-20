# Authentication

Primary source: [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md). See
also [../architecture/authentication.md](../architecture/authentication.md)
for the plugin-shape summary.

## Profiles

- `eir.config.json` — legacy development policy (`plugins/identity-local.ts`). Never a clinic deployment.
- `eir.demo.config.json` — strict workforce policy with local synthetic identities (`plugins/identity-staff-local.ts`, `localDevelopmentOnly: true`).
- `eir.clinic.config.example.json` — OIDC (`plugins/identity-oidc.ts`) + strict authorization + workforce administration + audit review.

## OIDC requirements

A confidential client supporting Authorization Code flow, S256 PKCE, RS256
ID tokens, and `client_secret_basic`. Exact registered callback
`https://your-host/auth/callback`. Exact issuer, client ID, origin, and an
approved `acr` assurance allowlist are all configured, not inferred. The
client secret is referenced by environment-variable name
(`clientSecretEnv`) — never committed.

## Validation performed

Issuer, audience, JWKS signature, expiration, nonce, state, PKCE,
authentication time, and assurance (`acr`). Browser-bound five-minute
single-use login transactions prevent replay and swapped-browser attacks.
Provider tokens are discarded after exchange; a separate opaque EHR session
token is hashed at rest and delivered via HttpOnly, `SameSite=Strict`
cookie (`Secure`/`__Host-` naming on HTTPS). Cookie-authenticated writes
also require an exact configured `Origin` header match.

## Session lifecycle

Maximum session lifetime 8 hours; server inactivity expiry configurable up
to 30 minutes (default 15). The default UI clears its chart after 15
minutes without API activity and on any `401`. Assignments are reloaded
every request — permission changes, expiry, and revocation are never cached
in the session token itself.

## Explicitly not implemented

IdP global/back-channel logout, multi-device session administration. The
loopback test IdP used in development signs real protocol messages but
establishes no national integration or independent security review.
