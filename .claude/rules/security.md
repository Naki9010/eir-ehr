# Security Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md) §14–§20. This file gives the security agent the detailed shape.

## Authentication

- Local dev identity (`plugins/identity-local.ts`): bearer tokens issued server-side, hashed at rest, held only in browser memory.
- OIDC (`plugins/identity-oidc.ts`): Authorization Code + S256 PKCE, RS256 ID tokens, `client_secret_basic`. The adapter validates issuer, audience, JWKS signature, expiration, nonce, state, PKCE, authentication time, and an approved `acr` assurance allowlist. Browser-bound transactions are five-minute, single-use (replay/swap prevention). Provider tokens are discarded after login; a separate opaque EHR session is issued.
- Session storage: HttpOnly, `SameSite=Strict` cookie, `__Host-` naming on HTTPS for OIDC; in-memory bearer token for local dev. Cookie-authenticated writes additionally require an exact configured `Origin` header (CSRF defense). Max session lifetime 8 hours; inactivity timeout configurable up to 30 minutes (default 15).
- The actual token issuance/validation logic lives in `packages/staff-sessions.ts`, not the identity plugins (which only call into it). Verified directly: tokens are 32-byte random values, SHA-256-hashed at rest, revalidated against the live workforce assignment on every use; `select()` only honors a client-supplied `assignmentId` if it belongs to the already-authenticated actor's own assignment set.
- The `localTestOnly` HTTP exception defaults to false and, even enabled, only accepts loopback endpoints — never relax this for a real deployment.

## Authorization

- Every clinical mutation, export, and AI call is action-specific — never a single "is clinician" boolean. Permissions are enumerated in `packages/workforce.ts`: documentation/signing, medication/reconciliation, lab order/receipt/review, scheduling, tasks, AI, export, oversight are separate grants.
- `Access.permit(actor, permission, patientId?)` (strict clinic policy) requires: active identity-matching assignment, the specific action permission, same provider/care unit, and an unexpired assignment-linked patient relationship. Restrictions (`patient.protected`) deny access outright — an unauthorized staff member gets no trace of the patient through directory, chart, history, change feed, or export.
- Client-supplied actor/role/tenant/organization/unit headers are always ignored; the server resolves the actor from the validated session on every request.
- Assignments are reloaded every request — never cached in a token, so revocation and expiry take effect immediately.
- Delegation (temporary access) requires existing access, `access.manage`, a named active clinician in that unit, a reason, and an expiry within 30 days and assignment validity. No self-grants, no automatic proxy grants.

## Exceptional ("break-glass") access

Requires a known internal patient ID, a reason, `access.emergency`, and chart-read permission. Lasts 15 minutes, read-only, unit-scoped. Never bypasses `patient.protected`. Grants no export, AI, or write capability. Always recorded as an auditable event. This is explicitly *not* a complete Swedish emergency-override or cross-provider consent mechanism — never describe it as one.

## Machine/integration principals

Authenticate only for their configured connector's result intake and receipt lookup — never `/api/session`, chart reads, signing, staff management, or AI. Attributed to `connector:<id>` with audit role `integration`. `integration.manage` is a separate administrative permission from clinical/workforce management, never auto-elevated on upgrade.

## Input handling

Request bodies capped at 128 KiB. Errors are `{error, fields?}` with status 401/403/404/409/422 — never leak a stack trace, SQL text, or internal path in a response. The server does not log request bodies, authorization headers, identifiers, or records (docs/OPERATIONS.md) — preserve this when adding any new logging.

## Multi-tenant/unit isolation

PostgreSQL uses role-bound row-level security per tenant; every provider-data table has forced RLS. RLS uses the authenticated role, not a request header or caller-set tenant variable. SQLite scopes by tenant in every query. A new table or query must be checked against both providers' isolation mechanism, not just one.

## What is explicitly NOT protected against (don't claim otherwise)

A privileged database administrator can still replace/truncate/recompute the audit-chain file or table — hash-chain verification is not external anchoring. Server plugins are trusted, privileged, in-process code; dependency declaration is not a sandbox against a malicious plugin. Untrusted third-party extensions would need a separate process/container that does not exist today.
