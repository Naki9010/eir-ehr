---
description: Focused security review of a change in Eir EHR — authentication, authorization, IDOR, tenant isolation, secrets, audit.
argument-hint: [optional path or feature name]
---

Run a security review of $ARGUMENTS (default: current diff against `main`) using the **security**, **backend**, **privacy**, and **audit** agents (CLAUDE.md §90).

Check specifically:

- **Authentication**: does every touched `/api` route still require a valid bearer session / OIDC cookie, with client-supplied actor/role/tenant headers ignored?
- **Authorization**: is every sensitive operation checked server-side against `packages/workforce.ts` permissions and `Access.check`/`Access.allowed`, never inferred from the frontend?
- **IDOR / privilege escalation**: can an authenticated actor reach another tenant's/unit's/patient's resource by supplying a different ID?
- **Tenant/patient isolation**: does the query or RLS policy actually scope by tenant/unit/patient, not just an application-layer `if`?
- **Secrets**: any hardcoded credential, token, or secret in source, config, or logs? Are new secrets injected via an environment-variable-named-by-config pattern?
- **Sessions**: does anything weaken HttpOnly/SameSite=Strict/Origin-check/PKCE/inactivity-timeout behavior?
- **Input validation / injection**: is every new input validated (schema, bounds, 128 KiB body limit) before reaching a domain method or query?
- **Data leakage**: does an error path or log line reveal a stack trace, SQL, secret, or unnecessary patient data?
- **Audit**: does every checked action have a corresponding audited entry?

Test the negative cases explicitly (CLAUDE.md §56): unauthenticated, wrong role, wrong organization, wrong unit, wrong patient, expired session, manipulated IDs, duplicate/concurrent request.

Report using CLAUDE.md §85 format. Do not modify code unless explicitly asked — this is a review.
