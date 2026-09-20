# Schema

Primary source: `plugins/storage-sqlite.ts` (schema literally embedded as
DDL, gated by `PRAGMA user_version`). PostgreSQL's schema
(`packages/postgres-migrations.ts`) is functionally equivalent with added
row-level security — see [../security/tenant-isolation.md](../security/tenant-isolation.md).

## SQLite tables (version 3 schema, as of this writing)

```sql
CREATE TABLE entities (
  id TEXT PRIMARY KEY, tenant TEXT NOT NULL, patientId TEXT NOT NULL,
  kind TEXT NOT NULL, version INTEGER NOT NULL,
  createdAt TEXT NOT NULL, updatedAt TEXT NOT NULL,
  data TEXT NOT NULL CHECK(json_valid(data))
);
CREATE INDEX chart ON entities(tenant, patientId, kind);
CREATE UNIQUE INDEX identifier_unique ON entities(
  tenant, json_extract(data,'$.identifier.system'), json_extract(data,'$.identifier.value')
) WHERE kind='patient';

CREATE TABLE versions (
  id TEXT NOT NULL, tenant TEXT NOT NULL, version INTEGER NOT NULL,
  snapshot TEXT NOT NULL, PRIMARY KEY(id, version)
);

CREATE TABLE grants (
  tenant TEXT NOT NULL, patientId TEXT NOT NULL REFERENCES entities(id),
  actorId TEXT NOT NULL, role TEXT NOT NULL, expires TEXT NOT NULL,
  PRIMARY KEY(tenant, patientId, actorId)
);

CREATE TABLE restrictions (
  tenant TEXT NOT NULL, patientId TEXT NOT NULL REFERENCES entities(id),
  blocked INTEGER NOT NULL, PRIMARY KEY(tenant, patientId)
);

CREATE TABLE sessions (hash TEXT PRIMARY KEY, actor TEXT NOT NULL, expires TEXT NOT NULL);

CREATE TABLE audit (
  seq INTEGER PRIMARY KEY AUTOINCREMENT, tenant TEXT NOT NULL,
  body TEXT NOT NULL, previous TEXT NOT NULL, hash TEXT NOT NULL
);

CREATE TABLE login_transactions (hash TEXT PRIMARY KEY, data TEXT NOT NULL, expires TEXT NOT NULL);
```

`entities.data` is validated JSON (`CHECK(json_valid(data))`) — the
generic-entity model (see [entities.md](entities.md)) means most clinical
"columns" (note text, observation code/value/unit, medication fields, etc.)
live inside this JSON payload rather than as dedicated relational columns,
validated at the application layer by typed schemas before insert.

## PostgreSQL differences

- Every provider-data table (including `sessions`, `login_transactions`,
  version and audit tables) has **FORCE RLS** keyed to the authenticated
  database role.
- The audit chain is appended only through a fixed-search-path
  `SECURITY DEFINER` function (`eir.append_audit`), not directly by the
  runtime role.
- A separate migration ledger (checksum-verified) tracks applied migrations,
  applied by a distinct operator credential — never the application runtime
  credential. See [migrations.md](migrations.md).

## PRAGMA-based startup guard

SQLite rejects opening a database with `user_version` newer than the
application understands (`throw new Error('Database version newer than
application')`) — a deliberate fail-closed behavior against running an old
binary against a newer schema.
