# API Changes

Filtered view of [CHANGELOG.md](CHANGELOG.md) for endpoint-affecting
entries — none of the changes below are marked breaking; each adds routes
behind newly-installed plugins rather than altering an existing route's
contract (see [../api/versioning.md](../api/versioning.md) for why there is
no URL version scheme to bump instead).

| Date | Routes added | Detail |
| --- | --- | --- |
| 2026-09-19 | `/coordination*` | [CHANGELOG](CHANGELOG.md#2026-09-19--add-modular-care-coordination-and-sip-workflows) |
| 2026-09-19 | `/modules`, `/deterioration*`, `/patients/:id/monitoring`, `/monitoring/:id/*`, `/deterioration-alerts/:id/respond` | [CHANGELOG](CHANGELOG.md#2026-09-19--add-optional-deterioration-monitoring-and-unit-level-module-controls) |
| 2026-09-19 | `/follow-up*` | [CHANGELOG](CHANGELOG.md#2026-09-19--add-clinical-follow-up-workflows-and-regulatory-evidence-brief) |
| 2026-09-19 | `/lab-connectors`, `/lab-orders/:id/delivery`, `/integrations/:connectorId/results` (outside `/api`) | [CHANGELOG](CHANGELOG.md#2026-09-19--add-reliable-laboratory-integration-runtime-and-operator-workflows) |
| 2026-09-19 | `/session/assignment`, `/workforce*`, `/access-review*`, `/patients/:id/emergency-access`, `/patients/:id/protection`, `/patients/:id/permissions` | [CHANGELOG](CHANGELOG.md#2026-09-19--add-staff-identity-clinic-authorization-and-access-review) |
| 2026-09-19 | `/patients/:id/medications`, `/medications/:id`, `/patients/:id/medication-reviews`, `/patients/:id/lab-orders`, `/lab-orders/:id/receive`, `/lab-orders/:id/review`, `/lab-orders/:id/cancel` | [CHANGELOG](CHANGELOG.md#2026-09-19--add-medication-reconciliation-and-owned-laboratory-result-review) |
| 2026-09-19 | `/care-team`, `/patients/:id/appointments`, `/appointments/:id/:action` | [CHANGELOG](CHANGELOG.md#2026-09-19--build-modular-care-team-scheduling-inbox-and-recoverable-drafts) |
| 2026-09-18 | Initial `/api` surface | [CHANGELOG](CHANGELOG.md#2026-09-18--build-sweden-first-modular-ehr-and-isolated-public-demo) |

Current full contract: [../api/endpoints.md](../api/endpoints.md) and
runtime `GET /api/openapi.json`.
