# Tenant Isolation

Primary source: [docs/PERSISTENCE.md](../PERSISTENCE.md) ("Provider
Isolation"), [docs/OPERATIONS.md](../OPERATIONS.md).

## PostgreSQL: role-bound row-level security

The PostgreSQL plugin is bound to **one** healthcare provider and one
database login. The operator provisions a database-side role-to-provider
mapping. Row-level security (RLS) uses the **authenticated database role**,
never a request header or caller-set session variable, to scope every
provider-data table — including sessions, login transactions, versions, and
audit metadata — under FORCE RLS.

The application refuses privileged runtime accounts: no superuser,
`BYPASSRLS`, ownership, role membership, or database/schema-creation rights.
The runtime cannot edit tenant mappings, migration metadata, audit heads, or
existing audit/version rows. A fixed-search-path database trigger
(`eir.append_audit`) appends the audit chain, not application code.

## SQLite: query-scoped tenancy

SQLite is single-process and single-tenant per database file (one file per
tenant in practice — e.g. one in-memory database per public-demo visitor).
Tenant scoping is enforced by always including `tenant` in queries
(`plugins/storage-sqlite.ts`), not by a database-level security feature —
this is why SQLite is documented as a local-development/demo provider, not
a multi-tenant production one.

## What tenant isolation does NOT protect against

A privileged database operator or a compromised/malicious plugin remains
inside the trust boundary — see
[../architecture/system-overview.md](../architecture/system-overview.md)
"Trust model". Multiple providers must use distinct runtime credentials;
configuring multiple providers under one runtime's workforce units is
explicitly unsupported (docs/PERSISTENCE.md).

## Cross-unit and cross-organization access

Within-provider care-unit, patient, and action authorization is a separate
layer from tenant RLS — see [access-control.md](access-control.md) and
[../clinical/clinical-governance.md](../clinical/clinical-governance.md).
Eir Samverkan's multi-unit coordination happens **within one tenant**, not
across independent provider databases — see
[../interoperability/integrations/README.md](../interoperability/integrations/README.md)
and [docs/SAMVERKAN.md](../SAMVERKAN.md) "Deployment boundary".
