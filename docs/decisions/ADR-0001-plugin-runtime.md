# ADR-0001 — Plugin Runtime With Declared Service Contracts

## Status
Accepted

## Date
2026-09-18 (initial release; documented in [docs/ARCHITECTURE.md](../ARCHITECTURE.md), [docs/PLUGINS.md](../PLUGINS.md))

## Context

Eir needs storage, identity, AI, terminology, and country-specific behavior
to all be replaceable — a Swedish primary-care deployment and a future
Danish/Estonian deployment share workflow logic but need different
identifiers, terminology, and possibly different storage. A monolithic
application with conditional country/provider branches would not scale to
this, and a full microservice architecture would be excessive for a single
deployable EHR core.

## Decision

Compose the application at startup from small plugins, each declaring
`id`, `version`, `apiVersion`, `provides`, and `requires` against shared
interfaces in `packages/contracts.ts`. A deployment profile (JSON list of
`{module, config}`) selects exactly one provider per service. The runtime
(`packages/runtime.ts`) resolves the dependency graph, rejects cycles and
duplicate providers, and exposes only declared dependencies to each plugin.

## Alternatives Considered

- A traditional monolith with feature flags/conditionals per country or
  storage backend — rejected as it would make replaceability implicit and
  fragile rather than an explicit, testable contract.
- A full microservice/process-per-plugin architecture — rejected as
  disproportionate operational complexity for a single-deployment EHR core,
  and it was explicitly noted this doesn't provide real isolation anyway
  unless plugins run in genuinely separate trust boundaries (see
  Security Impact).
- Adopting an existing plugin harness — the design is informed by
  [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)'s
  Cordis-based composition pattern, but Eir implements its own small typed
  runtime rather than depending on that harness.

## Why

Declared service contracts make "what does this plugin need/provide"
inspectable and enforceable at startup (`npm run plugins` prints the
resolved graph), and make swapping a provider (e.g. SQLite → PostgreSQL,
extractive AI → Ollama) a config change instead of a code change.

## Consequences

**Positive**: replaceability is real and tested (each replacement has a
required contract-test checklist, see docs/PLUGINS.md); adding a new
country pack or storage backend doesn't require touching domain logic.

**Negative**: the generic entity/service model pushes more validation into
application-level typed schemas rather than SQL-level constraints (see
[../database/entities.md](../database/entities.md)); there is no
compile-time guarantee a plugin correctly implements its declared
interface beyond TypeScript's structural typing plus contract tests.

## Security Impact

Dependency declaration is a **programming** contract, not a security
sandbox — this must be understood by every future security review (see
[ADR-0007](ADR-0007-no-untrusted-plugin-sandbox.md)).

## Privacy Impact

None directly — this is a composition mechanism, not a data-handling
decision.

## Clinical Impact

Enables country-specific clinical/terminology packs without forking the
core clinical workflow logic.

## Interoperability Impact

FHIR/HL7/integration providers plug into this same mechanism — see
[ADR-0002](ADR-0002-fhir-projection-not-server.md).

## Reliability Impact

No hot-swap of providers during live clinical writes — a provider change
always requires a controlled restart (see
[../devops/rollback.md](../devops/rollback.md)).
