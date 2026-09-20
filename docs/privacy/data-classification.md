# Data Classification

## Categories present in the system

| Category | Examples | Where it lives |
| --- | --- | --- |
| Direct patient identifiers | Name, birth date, personnummer/samordningsnummer, local ID | `entities` table, `kind='patient'` |
| Clinical content | Notes, observations, diagnoses, allergies, medications, lab reports | `entities`/`versions` tables, all kinds |
| Access/authorization metadata | Assignments, permissions, care relationships, sessions | Workforce and session tables |
| Audit metadata | Actor, action, patient/entity reference, outcome, hash chain | `audit` table |
| Operational/staff data | Staff names, professions, roster entries | Care-team configuration and workforce entities |
| Coordination metadata (Samverkan) | Case/message/SIP content, no chart `patientId` | Private `sam*` entity kinds, excluded from chart/FHIR |
| Secrets | Session hashes, OIDC client secret, connector tokens, backup key | Environment variables and hashed session store only |

No formal data classification policy document (e.g. labelled sensitivity
tiers with handling rules per tier) exists yet as a standalone artifact —
this table is the closest current inventory, derived from the schema and
contracts, not from a separately approved classification scheme. See
[../database/entities.md](../database/entities.md) for the exact entity
kinds.

## Sensitivity note on coordination/monitoring metadata

Deterioration-monitoring snapshots omit direct patient names/identifiers
from the payload sent to an external risk model, but source references,
timestamps, and laboratory text remain sensitive patient data — this is
explicitly **not** anonymization (docs/DETERIORATION.md "External model
API"). Treat it with the same sensitivity as clinical content.
