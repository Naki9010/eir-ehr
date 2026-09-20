# Indexes

Primary source: `plugins/storage-sqlite.ts`.

| Index | Table | Purpose |
| --- | --- | --- |
| `chart` | `entities(tenant, patientId, kind)` | The primary chart-read access pattern — fetch all entities of given kinds for a patient |
| `identifier_unique` | `entities(tenant, json_extract(data,'$.identifier.system'), json_extract(data,'$.identifier.value')) WHERE kind='patient'` | Prevents duplicate patient identifiers within a tenant (partial/filtered unique index) |
| Primary keys | `versions(id, version)`, `grants(tenant, patientId, actorId)`, `restrictions(tenant, patientId)`, `sessions(hash)`, `login_transactions(hash)` | Composite primary keys double as covering indexes for their respective lookup patterns |
| `audit(seq)` | `audit` | Auto-incrementing integer primary key is the natural append-order index used by cursor pagination |

## Queue-specific indexes (integration runtime)

PostgreSQL migration version 2 (see [migrations.md](migrations.md)) adds
dedicated queue indexes and uniqueness constraints for the lab-integration
outbox/inbox tables to support bounded, keyset-paginated
`searchEntities` scans without full-table scans — see
[docs/INTEGRATIONS.md](../INTEGRATIONS.md) "Modules": "SQLite/PostgreSQL
implement bounded database-side filtering and keyset pagination."

## Gap

There is no documented index-usage/query-plan audit in this repository. As
the entity model is generic (see [entities.md](entities.md)), most
`kind`-specific filtering happens after the `chart` index narrows to
`(tenant, patientId, kind)` — a query filtering by a field *inside* `data`
without also constraining `patientId` would not benefit from an index and
should be treated as a potential performance risk if ever introduced.
