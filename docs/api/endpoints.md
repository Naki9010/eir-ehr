# Endpoints (Grouped Summary)

Full, authoritative table with exact payload shapes: [docs/API.md](../API.md).
This page groups endpoints by concern for navigation.

## Session and platform

`GET /session`, `POST /logout`, `GET /plugins`, `GET /openapi.json`,
`GET /terminology/diagnoses`.

## Clinical core

`GET/POST /patients`, `GET /patients/:id/chart`,
`POST /patients/:id/records/:kind`, `POST /records/:id/:action`,
`GET /records/:id/history`, `GET /patients/:id/changes`,
`GET /patients/:id/export/fhir`.

## AI review

`POST /patients/:id/ai`, `POST /proposals/:id/review`.

## Access

`POST /patients/:id/access`, `POST /patients/:id/restriction`,
`GET /patients/:id/permissions`, `POST /patients/:id/emergency-access`,
`POST /patients/:id/protection`.

## Workforce and audit

`POST /session/assignment`, `GET/POST /workforce`, `POST /workforce/:id`,
`GET/POST /access-review`, `GET /audit`.

## Care team

`GET /care-team`, `POST /patients/:id/appointments`,
`POST /appointments/:id/:action`, task creation/transition routes (see
[docs/API.md](../API.md) "Care-team additions").

## Medications and laboratory (see [../clinical/medications.md](../clinical/medications.md), [../clinical/laboratory-results.md](../clinical/laboratory-results.md))

`GET/POST /patients/:id/medications`, `POST /medications/:id`,
`POST /patients/:id/medication-reviews`, `POST /patients/:id/lab-orders`,
`POST /lab-orders/:id/receive`, `POST /lab-orders/:id/review`,
`POST /lab-orders/:id/cancel`.

## Optional-module routes (only advertised when the plugin is installed)

Follow-up (`/follow-up*`), deterioration (`/modules`, `/deterioration*`,
`/patients/:id/monitoring`, `/monitoring/:id/*`,
`/deterioration-alerts/:id/respond`), coordination
(`/coordination*`), integrations
(`/lab-connectors`, `/lab-orders/:id/delivery`,
`/integrations/:connectorId/results` — **outside** `/api`).

## Discovery

`GET /api/openapi.json` is generated from the same request-validation
schemas that enforce these routes and only lists routes for plugins actually
installed in the running profile — it is the single source of truth for the
*exact* currently-active contract, more precise than any static document
including this one.
