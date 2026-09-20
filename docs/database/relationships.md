# Relationships

## Patient-centric scoping

Every entity carries `patientId` directly (not via a join table), and every
read/write path is scoped by `tenant` + `patientId` (+ `kind` for the chart
index). This is why `CREATE INDEX chart ON entities(tenant, patientId,
kind)` exists — it's the primary access pattern.

## Encounter scoping

Clinical creation for note/observation/condition/allergy/task references an
`encounterId` inside the entity's `data` payload; the domain service
(`plugins/clinical.ts`) checks that the referenced encounter is open and
belongs to the same patient **before** allowing the write — this check is
application logic, not a SQL foreign key, because `data` is opaque JSON at
the storage layer.

## Version-to-entity relationship

`versions(id, version)` is a append-only history table keyed to
`entities.id` — every revision of an entity is preserved as a full snapshot,
not a diff. `Store.history(tenant, id)` returns the complete chain. This is
what makes amendments/corrections non-destructive (see
[../clinical/clinical-notes.md](../clinical/clinical-notes.md)).

## Cross-entity linking (task ↔ lab order)

`CareTeam.createLinkedTask`/`syncLinkedTask` create an application-level
link between a follow-up task and a lab order (or deterioration alert),
recorded inside the task's `data` payload (e.g. a `labOrderId` or
`deteriorationAlertId` field) — this link is what lets generic task-
completion logic detect and block bypassing a required clinical review (see
[../clinical/laboratory-results.md](../clinical/laboratory-results.md)).

## Foreign keys that do exist at the SQL level

`grants.patientId REFERENCES entities(id)` and
`restrictions.patientId REFERENCES entities(id)` — both enforced with
`PRAGMA foreign_keys=ON` in SQLite. Most other relationships (encounter
ownership, task-to-order links, coordination case membership) are enforced
in application code against the JSON payload, not via SQL foreign keys,
because of the generic-entity model described in [entities.md](entities.md).
