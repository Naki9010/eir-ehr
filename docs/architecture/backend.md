# Architecture: Backend

Primary source: [docs/ARCHITECTURE.md](../ARCHITECTURE.md),
[packages/contracts.ts](../../packages/contracts.ts). Full API documentation:
[../api/README.md](../api/README.md).

## Layering

```
HTTP (apps/http.ts, apps/app.ts, Fastify)
  → Domain services behind packages/contracts.ts interfaces
      (Clinical, Access, Workforce, Medications, Laboratories, CareTeam,
       AIReview, Fhir, Integrations, FollowUp, Deterioration, Coordination…)
  → Store (plugins/storage-sqlite.ts | plugins/storage-postgres.ts)
```

Controllers stay thin: authentication resolves the actor from the bearer
session (or OIDC cookie), authorization is delegated to `Access`/`Workforce`
inside each domain service, validation uses typed schemas
(`packages/*.ts`, mostly Zod), and errors are raised as the shared `Fault`
class (`packages/contracts.ts`) with an HTTP status and message — never a raw
exception or SQL error surfaced to the client. See
[../api/errors.md](../api/errors.md).

## Domain plugins (in `plugins/`)

Each implements one or more `Services` keys and depends only on its declared
`requires`. Representative examples: `clinical.ts` (encounters/notes/
observations/conditions/allergies/tasks — see `Clinical` in contracts.ts),
`medications.ts`, `laboratories.ts`, `care-team.ts`, `ai-review.ts` +
`ai-extractive.ts`/`ai-ollama.ts`, `fhir-r4.ts`, `integrations.ts`,
`follow-up.ts`, `deterioration.ts`, `coordination.ts` and its siblings
(`coordination-directory.ts`, `coordination-documents.ts`,
`coordination-notifications.ts`, `coordination-payment.ts`, `sip-plans.ts`),
`access.ts`/`access-clinic.ts`/`access-review.ts`, `workforce.ts`,
`identity-local.ts`/`identity-oidc.ts`/`identity-staff-local.ts`,
`country-se.ts`/`country-eu-local.ts`, `terminology-icd-se.ts`,
`modules.ts`, `risk-vitals.ts`/`risk-http.ts`, `notification-http.ts`,
`lab-transport-http.ts`.

## Transactions

`Store.transaction(async () => ...)` is the unit of work. A domain mutation,
its version snapshot, and its audit write commit together — a failed
operation never leaves a partial record. Transaction callbacks must be
database-only and replayable: no network calls, model inference, or other
external effects inside them (PostgreSQL may retry the callback on
serialization failure). See [../database/transactions.md](../database/transactions.md)
and [docs/PERSISTENCE.md](../PERSISTENCE.md).

## Concurrency and queues

Background processing (lab integration, follow-up, deterioration,
coordination) runs as separate worker processes (`apps/*-worker.ts`) using
leased, retryable queue rows in the same store — not Redis/Kafka. See
[../reliability/queues.md](../reliability/queues.md).
