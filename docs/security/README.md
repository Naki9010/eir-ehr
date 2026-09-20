# Security Documentation

Primary source: [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md),
[docs/OPERATIONS.md](../OPERATIONS.md), [.claude/rules/security.md](../../.claude/rules/security.md).

## Contents

- [threat-model.md](threat-model.md) — assets/actors/trust boundaries/threats/controls
- [authentication.md](authentication.md) — OIDC and local identity plugins, session model
- [authorization.md](authorization.md) — permission model, policy enforcement
- [access-control.md](access-control.md) — care-relationship/unit/action checks in practice
- [tenant-isolation.md](tenant-isolation.md) — PostgreSQL RLS and SQLite tenant scoping
- [secrets.md](secrets.md) — how credentials are configured and protected
- [api-security.md](api-security.md) — input validation, rate limiting, headers, CSRF/origin checks
- [dependency-security.md](dependency-security.md) — `npm audit` in CI, plugin trust model
- [security-decisions/](security-decisions/README.md) — security-scoped ADRs

## The trust-model caveat (repeat it in every review)

Server plugins and the frontend bundle are reviewed, operator-installed,
privileged in-process code — dependency declarations are a programming
contract, not a sandbox. Every control documented here assumes an honest
plugin set and an uncompromised host/database administrator. See
[../architecture/system-overview.md](../architecture/system-overview.md)
"Trust model" and CLAUDE.md §19–§20.

## Status

Working security controls exist and are covered by automated tests (see
[../testing/security-tests.md](../testing/security-tests.md)). No
independent penetration test, external security certification, or
production-hardened deployment has been performed — see
[docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md) "Clinical release
gates" for exactly what remains before real patient data could be
considered.
