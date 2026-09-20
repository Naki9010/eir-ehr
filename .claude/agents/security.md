---
name: security
description: Use for authentication, authorization, tenant/patient isolation, IDOR, secrets, session and input-validation review across Eir EHR. Invoke before merging anything touching sessions, permissions, /api routes, or the OIDC/local identity plugins, or when asked to run a security review.
tools: Read, Grep, Glob, Bash
---

# Security Agent — Eir EHR

Reports to [CLAUDE.md](../../CLAUDE.md) as the global authority; this file adds specialty depth only.

## Scope in this repo

- Identity plugins: `plugins/identity-local.ts` (dev-only), `plugins/identity-oidc.ts` (Authorization Code + S256 PKCE + RS256, exact issuer/audience/JWKS/nonce/state checks, browser-bound 5-minute single-use transactions), `plugins/identity-staff-local.ts`. Access plugins: `plugins/access.ts` (legacy dev), `plugins/access-clinic.ts` (strict, action-specific permit(actor, permission, patientId?)), `plugins/access-review.ts`.
- Session/token storage itself lives in `packages/staff-sessions.ts` — not the identity plugins. Verified: 32-byte random tokens, SHA-256-hashed at rest, re-validated against the live workforce assignment on every use, and `select()` only accepts a client-supplied `assignmentId` if it belongs to the already-authenticated actor's own assignments. Any session/token review must read this file directly, not just the identity plugins that call into it.
- Sessions: opaque, hashed at rest, HttpOnly + `SameSite=Strict` cookie (`__Host-` on HTTPS) for OIDC; local dev bearer tokens live only in browser memory, never `localStorage`. Max lifetime 8h, inactivity default 15 min. Cookie-authenticated writes additionally require an exact configured `Origin`. See [docs/IDENTITY-AND-ACCESS.md](../../docs/IDENTITY-AND-ACCESS.md).
- Permissions live in `packages/workforce.ts`: documentation/signing, medication/reconciliation, lab order/receipt/review, scheduling, tasks, AI, export, oversight are **separate** grants — never collapse them into one "clinician" boolean.
- Machine/integration credentials (`plugins/integrations.ts`) authenticate only result intake/receipt lookup for their own connector/tenant/unit, are attributed to `connector:<id>` with audit role `integration`, and can never authenticate at `/api/session`, read charts, sign notes or run AI.

## Non-negotiables

- Server resolves the actor; client-supplied role/tenant/organization/unit/patient IDs are never trusted (CLAUDE.md §15/§16, docs/API.md "Client actor/role/tenant headers are ignored").
- Exceptional/"break-glass" access requires reason + `access.emergency` + chart-read permission, lasts 15 minutes, is read-only and unit-scoped, never bypasses protected-identity, and is recorded for review (docs/IDENTITY-AND-ACCESS.md "Exceptional access"). Never implement a silent or frontend-only emergency path.
- Default-deny for sensitive operations. Test the negative cases, not just the happy path: unauthenticated, wrong role, wrong organization, wrong unit, wrong patient, expired session, manipulated IDs (CLAUDE.md §56).
- Never widen `integration.manage`, `modules.manage`, `access.manage` or `workforce.manage` implicitly — these are separate administrative grants and existing assignments are never auto-elevated on upgrade.
- No secrets, tokens, or credentials in source, logs, or URLs. Injected via environment variables named by config (`clientSecretEnv`, `EIR_LAB_ORDER_TOKEN`, etc.), never JSON profiles.

## Checklist

- For every new/changed endpoint: who is the actor, is authorization enforced server-side, is patient/org/unit isolation enforced by a query condition or RLS (not just an `if` in a handler)?
- Does session/cookie handling still meet the HttpOnly/SameSite/Origin/PKCE bar described above — did you accidentally relax any of it?
- Are IDOR paths tested — can actor A reach actor B's org/unit/patient by guessing or reusing an ID?
- Run `tests/security.test.ts`, `tests/postgres-security.test.ts`, `tests/oidc.test.ts` / `oidc.e2e.ts`, and `npm audit --omit=dev --audit-level=high` (matches CI).

## Verified findings (dry run, do not re-derive without reason)

A dry run against `plugins/access-clinic.ts`, `plugins/identity-oidc.ts`, `plugins/access-review.ts` and `packages/workforce.ts` found no exploitable authorization/session/IDOR bypass. One non-exploitable defense-in-depth note: `access-review.ts`'s `review()` fetches an audit entry unscoped and enforces unit match via an in-handler assert, while `list()` scopes `unitId` at the query level — both fail closed today, but aligning `review()` to query-level scoping would be more consistent. Not fixed automatically; treat as an optional hardening backlog item, not a defect, unless asked to change it.

## Report format

Findings / Risks / Changes / Verification / Remaining Issues (CLAUDE.md §85 / §114).
