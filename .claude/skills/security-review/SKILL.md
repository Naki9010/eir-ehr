---
name: security-review
description: Run a focused security pass on an Eir EHR change — authentication, authorization, IDOR, tenant/patient isolation, secrets, session handling. Use before merging anything touching /api routes, identity/access plugins, or credentials.
---

# Security Review

See `.claude/rules/security.md` for the full detail this skill draws from.

## Fast checklist

1. Does every touched route require a valid session (bearer or OIDC cookie), with client-supplied actor/role/tenant headers ignored?
2. Is authorization checked server-side via `packages/workforce.ts` permissions / `Access.check`/`Access.allowed` — never inferred from what the frontend shows or hides?
3. Can an authenticated actor reach another tenant's, unit's, or patient's resource by supplying a different ID (IDOR)? Trace the actual query/RLS, don't assume.
4. Are new secrets/tokens injected via an environment-variable-named-by-config pattern, never inline in JSON, source, or logs?
5. Does anything weaken HttpOnly / `SameSite=Strict` / Origin-check / PKCE / inactivity-timeout behavior?
6. Is every new input validated (schema, bounds, the 128 KiB body cap) before reaching a domain method?
7. Does an error path leak a stack trace, SQL, secret, or unnecessary patient data?
8. Does the action have a corresponding audit write in the same transaction?
9. Is `integration.manage`/`modules.manage`/`access.manage`/`workforce.manage` still separate from ordinary clinical/admin grants, never auto-elevated?

## Negative-case test list

Unauthenticated, wrong role, wrong organization, wrong unit, wrong patient, expired session, manipulated IDs, duplicate/concurrent request — see `tests/security.test.ts` and `tests/postgres-security.test.ts` for the existing pattern to extend.

## Reporting

List each finding with file/line, the concrete exploit scenario (who does what to get unauthorized access to what), and the fix — don't report "looks secure" without walking through at least the IDOR and authorization-bypass angles explicitly.
