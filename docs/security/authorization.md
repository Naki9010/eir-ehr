# Authorization

Primary source: [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md). See
also [../architecture/authorization.md](../architecture/authorization.md) for
the contract/permission-list summary — not repeated here.

## Enforcement points

Every clinical mutation, export, and AI call is checked against a specific
permission from the 24-entry `Permission` enum
(`packages/contracts.ts`) via `Access.permit`/`Access.check`, inside the
domain service — never only in the frontend, and never inferred from a
client-supplied role/tenant/unit value. `plugins/access-clinic.ts` requires:
an active identity-matching assignment, the specific action permission, the
same provider/care unit, and an unexpired assignment-linked patient
relationship.

## Delegation and coverage

Delegation requires existing access, `access.manage`, a named active
clinician in the same unit, a reason, and an expiry within 30 days and
assignment validity. There are no self-grants and no automatic proxy
grants. Follow-up coverage (`task.write`) never itself grants patient
access — every automatic handover rechecks current workforce assignment,
unit, clinical permission, and care relationship at the moment of handover.
See [docs/FOLLOW-UP.md](../FOLLOW-UP.md) "Authorization And Coverage".

## Emergency access

Requires a known internal patient ID, reason, `access.emergency`, and
chart-read permission. Lasts 15 minutes, is read-only and unit-scoped, never
bypasses restrictions, and still requires `patient.protected` for a
protected patient. No export, AI, or write action is authorized by the
exception. Generates an audit event. **This is not a complete Swedish
emergency override or cross-provider consent mechanism.**

## Authorization test coverage

See [../testing/security-tests.md](../testing/security-tests.md) and the
security test matrix (CLAUDE.md §56): authenticated, unauthenticated, wrong
role, wrong organization, wrong unit, wrong patient, expired session,
manipulated IDs are all explicitly tested scenarios in `tests/workforce.test.ts`,
`tests/security.test.ts`, `tests/postgres-security.test.ts`.
