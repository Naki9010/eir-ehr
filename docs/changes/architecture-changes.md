# Architecture Changes

Filtered view of [CHANGELOG.md](CHANGELOG.md) for entries with architectural
impact (new plugins, new service contracts, runtime-API changes).

| Date | Change | Detail |
| --- | --- | --- |
| 2026-09-19 | PostgreSQL storage provider + runtime API v2 (async storage contract) | [CHANGELOG](CHANGELOG.md#2026-09-19--add-persistent-postgresql-clinic-backend-and-recovery) |
| 2026-09-19 | `modules`/`riskEngine`/`deterioration` service contracts | [CHANGELOG](CHANGELOG.md#2026-09-19--add-optional-deterioration-monitoring-and-unit-level-module-controls) |
| 2026-09-19 | `followUp`/`followUpPolicy`/`notificationTransport` service contracts | [CHANGELOG](CHANGELOG.md#2026-09-19--add-clinical-follow-up-workflows-and-regulatory-evidence-brief) |
| 2026-09-19 | `integrations`/`labTransport` service contracts | [CHANGELOG](CHANGELOG.md#2026-09-19--add-reliable-laboratory-integration-runtime-and-operator-workflows) |
| 2026-09-19 | `workforce`/`accessReview` service contracts, strict `access-clinic` policy | [CHANGELOG](CHANGELOG.md#2026-09-19--add-staff-identity-clinic-authorization-and-access-review) |
| 2026-09-19 | `careTeam` service contract | [CHANGELOG](CHANGELOG.md#2026-09-19--build-modular-care-team-scheduling-inbox-and-recoverable-drafts) |
| 2026-09-18 | Initial plugin runtime, `packages/contracts.ts`, core service set | [CHANGELOG](CHANGELOG.md#2026-09-18--build-sweden-first-modular-ehr-and-isolated-public-demo) |

See [../architecture/README.md](../architecture/README.md) for the current
state each of these changes produced, and
[../architecture/decisions/README.md](../architecture/decisions/README.md)
for the ADRs behind the largest of them.
