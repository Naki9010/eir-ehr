# Database Changes

Filtered view of [CHANGELOG.md](CHANGELOG.md) for schema/migration-relevant
entries.

| Date | Change | Migration | Detail |
| --- | --- | --- | --- |
| 2026-09-19 | Full PostgreSQL schema, FORCE RLS, operator-only migration ledger | Initial PostgreSQL schema | [CHANGELOG](CHANGELOG.md#2026-09-19--add-persistent-postgresql-clinic-backend-and-recovery) |
| 2026-09-19 | Lab-integration queue tables, uniqueness, immutable-message guards | PostgreSQL migration v2 | [CHANGELOG](CHANGELOG.md#2026-09-19--add-reliable-laboratory-integration-runtime-and-operator-workflows) |
| 2026-09-19 | Session inactivity tracking, durable login transactions | SQLite schema v2 | [CHANGELOG](CHANGELOG.md#2026-09-19--add-staff-identity-clinic-authorization-and-access-review) |
| 2026-09-19 | New `medicationStatement`/`labOrder`/`labReport` entity kinds | None (generic entity model) | [CHANGELOG](CHANGELOG.md#2026-09-19--add-medication-reconciliation-and-owned-laboratory-result-review) |
| 2026-09-19 | New `sam*` coordination entity kinds | None (generic entity model) | [CHANGELOG](CHANGELOG.md#2026-09-19--add-modular-care-coordination-and-sip-workflows) |
| 2026-09-19 | New deterioration monitoring/alert/assessment kinds | None (generic entity model) | [CHANGELOG](CHANGELOG.md#2026-09-19--add-optional-deterioration-monitoring-and-unit-level-module-controls) |
| 2026-09-18 | Initial SQLite schema (`entities`, `versions`, `grants`, `restrictions`, `sessions`, `audit`) | Initial schema | [CHANGELOG](CHANGELOG.md#2026-09-18--build-sweden-first-modular-ehr-and-isolated-public-demo) |

See [../database/schema.md](../database/schema.md) for current state and
[../database/migrations.md](../database/migrations.md) for the migration
mechanism itself.
