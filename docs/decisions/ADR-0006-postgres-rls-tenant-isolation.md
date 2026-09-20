# ADR-0006 — Enforce Tenant Isolation Via PostgreSQL Role-Bound RLS

## Status
Accepted

## Date
2026-09-19 (per [docs/PERSISTENCE.md](../PERSISTENCE.md) "Provider Isolation")

## Context

A persistent multi-process PostgreSQL deployment needs a strong guarantee
that one provider's runtime process cannot read or write another
provider's rows, even under an application-layer bug — application-layer
`WHERE tenant = ?` filtering alone is a single point of failure if any
query anywhere forgets that clause.

## Decision

Bind each PostgreSQL runtime role to exactly one healthcare provider via a
database-side role-to-provider mapping, and enforce **FORCE ROW LEVEL
SECURITY** on every provider-data table (including sessions, login
transactions, versions, and audit metadata), keyed to the authenticated
database role — never a request header or a caller-set session variable.
Refuse privileged runtime accounts (no superuser, `BYPASSRLS`, ownership,
role-membership, or schema-creation rights).

## Alternatives Considered

- Application-layer-only tenant filtering — rejected as the sole
  mechanism; it was kept as a second, defense-in-depth layer (see
  [../security/tenant-isolation.md](../security/tenant-isolation.md)), but
  RLS is the authoritative boundary because it holds even against a bug in
  application query-building code.
- A separate PostgreSQL database per tenant — considered simpler to reason
  about, but rejected for this release as it would complicate connection
  management and cross-cutting operator tooling (migrations, backups) for
  the current deployment model; each provider does still get its own
  restricted login and role.

## Why

RLS keyed to the authenticated role (not application-supplied data) means
a compromised or buggy query — or a compromised application process using
its own restricted credential — still cannot read another tenant's rows,
because the database itself, not the application, enforces the boundary.

## Consequences

**Positive**: tenant isolation survives an entire class of application-
layer bugs.

**Negative**: multiple providers cannot share one runtime's workforce
units or one runtime process — each provider needs its own distinct
runtime credential (docs/PERSISTENCE.md), which is an explicit operational
constraint operators must follow.

## Security Impact

This is the security decision — see
[../security/tenant-isolation.md](../security/tenant-isolation.md) for the
full treatment.

## Privacy Impact

Directly reduces cross-provider PHI-exposure risk from an application bug.

## Clinical Impact

None beyond the general patient-safety benefit of stronger tenant
isolation.

## Interoperability Impact

None.

## Reliability Impact

None beyond ordinary database-role-management operational overhead.
