# Entities

Primary source: `packages/contracts.ts` (`Entity` type), `plugins/clinical.ts`
and sibling domain plugins.

## The generic entity model

```ts
type Entity = {
  id: string; tenant: string; patientId: string; kind: string;
  version: number; createdAt: string; updatedAt: string;
  data: Record<string, any>;
};
```

Every clinical and operational record in Eir — a patient, an encounter, a
note, an observation, a condition, an allergy, a task, a medication
statement, a lab order/report, an appointment, a coordination case, a
follow-up/deterioration record — is stored through this same shape. There is
no per-clinical-type relational table; `kind` discriminates the record type
and `data` holds its type-specific payload, validated by a typed schema
(mostly Zod, in the relevant `packages/*.ts`) at the API boundary before
`Store.insert`/`revise` is called.

## Known `kind` values in use (non-exhaustive, derived from domain plugins)

`patient`, `encounter`, `note`, `observation`, `condition`, `allergy`,
`task`, `medicationStatement`/equivalent, `labOrder`, `labReport` (via
`packages/lab-application.ts`), `appointment`, `proposal` (AI review),
coordination's private `sam*`-prefixed kinds (case, message, SIP plan,
attachment — see `packages/coordination.ts`), deterioration monitoring/
alert/assessment kinds (`packages/deterioration.ts`), and follow-up
coverage/notification kinds (`packages/follow-up.ts`). The exact literal
strings are defined per domain plugin, not centrally enumerated — treat this
list as a map to the relevant `packages/*.ts` file, not a database-level
enum.

## Why a generic model

This design lets every `kind` share the same version/audit/transaction
machinery (see [transactions.md](transactions.md)) without a schema
migration per new clinical concept — adding a new entity kind is an
application-level change (new Zod schema + new command handlers), not a
`CREATE TABLE`. The tradeoff, made deliberately, is that relational
constraints beyond `patientId`/`tenant`/uniqueness live in application code
and typed schemas rather than SQL foreign keys for most fields.
