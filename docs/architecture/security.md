# Architecture: Security (cross-cutting view)

This is the architectural summary; the full security documentation set lives
under [../security/](../security/README.md), and the detailed narrative is in
[docs/OPERATIONS.md](../OPERATIONS.md) ("Threats And Enforced Controls").

## Where security lives in the architecture

- **Authentication** resolves the actor server-side from a session/OIDC
  token; client-supplied actor/role/tenant headers are always ignored
  ([authentication.md](authentication.md)).
- **Authorization** is enforced in domain-service code via `Access`/
  `Workforce`, never in the frontend or at the routing layer alone
  ([authorization.md](authorization.md)).
- **Tenant/patient isolation** is enforced at the storage layer for
  PostgreSQL (role-bound RLS) and at the query layer for SQLite (tenant-
  scoped queries), in addition to the domain-service checks above — defense
  in depth, not a single control (see [../security/tenant-isolation.md](../security/tenant-isolation.md)).
- **Secrets** are environment variables referenced by name from JSON
  profiles (`clientSecretEnv`, `tokenEnv`, etc.), never embedded in profile
  JSON or committed ([../security/secrets.md](../security/secrets.md)).
- **Input validation** is centralized in typed schemas (mostly Zod) in
  `packages/*.ts`, applied before any domain logic runs.

## The one fact every reviewer must internalize

Plugins are trusted, privileged, in-process code (see
[system-overview.md](system-overview.md) "Trust model"). Every security
control described in this documentation set assumes an honest, reviewed
plugin set and a non-compromised host/database administrator. It is **not**
a defense against a malicious plugin, a compromised dependency, or a
privileged operator — that requires process isolation Eir does not currently
provide.
