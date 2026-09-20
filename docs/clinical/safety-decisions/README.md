# Clinical-Safety-Scoped ADRs

Pointer into [../../decisions/](../../decisions/README.md) for decisions with
primarily clinical-safety impact.

| ADR | Title | Status |
| --- | --- | --- |
| [ADR-0004](../../decisions/ADR-0004-note-immutability-via-db-trigger.md) | Enforce signed-note immutability at the database layer, not only in application code | Accepted |
| [ADR-0005](../../decisions/ADR-0005-no-patient-merge.md) | Do not implement patient merge/split until a dedicated design exists | Accepted |

No ADR currently exists for the deterioration-alert resolution workflow or
the lab-review linked-task guard; these are candidates for a future ADR if
their design is revisited. See
[../../decisions/README.md](../../decisions/README.md) for the full list.
