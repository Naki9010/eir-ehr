# Eir EHR Documentation

This is the documentation index for Eir EHR, an Apache-2.0, Sweden-first electronic
health record built from replaceable plugins (see repository [README.md](../README.md)).

**Status as of this writing:** working development EHR, version `0.2.0`, synthetic
data only. It is not a complete production EHR, not a certified Swedish
national-service client, and not EHDS-conformant. See
[docs/PROJECT-STATUS.md](PROJECT-STATUS.md) and [docs/PLAN.md](PLAN.md) for the
full evidence-based account of what is implemented versus planned.

## How this documentation is organized

Two layers exist side by side, deliberately, rather than one replacing the other:

- **`docs/*.md` (flat, pre-existing).** These are the project's original,
  detailed, evidence-cited references — e.g. [ARCHITECTURE.md](ARCHITECTURE.md),
  [API.md](API.md), [FHIR.md](FHIR.md), [PERSISTENCE.md](PERSISTENCE.md),
  [IDENTITY-AND-ACCESS.md](IDENTITY-AND-ACCESS.md). They remain the primary,
  most detailed source for their subjects and are linked to, not duplicated,
  throughout the hierarchy below.
- **`docs/<category>/` (this hierarchy).** A structured, navigable system
  required by the project's documentation policy (root [CLAUDE.md](../CLAUDE.md)).
  Each category below gives a focused, current account of one concern (a change
  log, a database schema description, a security control list) and points into
  the flat docs for full detail rather than re-deriving it.

Anything stated as fact in this hierarchy is either quoted/derived from a flat
doc, or traced to a specific source file or test. Where something is not yet
implemented or not verified, it is marked as such explicitly — never presented
as done. See [CLAUDE.md §112](../CLAUDE.md) for the precise verification
language this documentation follows (Implemented / Configured / Connected /
Tested / Verified / Validated / Certified are distinct states).

## Categories

| Category | Purpose |
| --- | --- |
| [architecture/](architecture/README.md) | System shape: plugin runtime, composition roots, layering, cross-cutting concerns |
| [clinical/](clinical/README.md) | Clinical record model, workflows, patient safety, clinical governance boundary |
| [interoperability/](interoperability/README.md) | FHIR export, HL7 (not implemented), external integrations |
| [security/](security/README.md) | AuthN/authZ, tenant isolation, secrets, API security, threat model |
| [privacy/](privacy/README.md) | Data classification, data flows, PHI handling, logging/telemetry, retention |
| [database/](database/README.md) | Schema, entities, relationships, migrations, concurrency |
| [api/](api/README.md) | HTTP API contract: endpoints, authN/authZ, errors, versioning |
| [frontend/](frontend/README.md) | Browser workspace: navigation, patient context, components, accessibility |
| [ai/](ai/README.md) | AI proposal/review pipeline: providers, evidence, human review, safety |
| [testing/](testing/README.md) | Test strategy and the actual suites that exist |
| [reliability/](reliability/README.md) | Failure modes, retry/idempotency, queues, recovery, monitoring |
| [devops/](devops/README.md) | Environments, local dev, Docker, CI/CD, deployment, configuration |
| [features/](features/README.md) | Per-feature documentation (purpose, workflow, architecture, safety, tests) |
| [changes/](changes/README.md) | CHANGELOG and area-specific change logs |
| [decisions/](decisions/README.md) | Architecture Decision Records (ADRs) |
| [releases/](releases/README.md) | Release documentation |
| [audits/](audits/README.md) | Security/privacy/clinical-safety/interoperability/release audit reports |
| [traceability.md](traceability.md) | Requirement → feature → architecture → API → database → security → clinical safety → tests → release matrix |

## Reading order for newcomers

1. [architecture/system-overview.md](architecture/system-overview.md) — what Eir is built from.
2. [docs/PROJECT-STATUS.md](PROJECT-STATUS.md) — what actually works today.
3. [clinical/patient-safety.md](clinical/patient-safety.md) and
   [security/README.md](security/README.md) — the two hardest constraints.
4. [api/README.md](api/README.md) and [database/README.md](database/README.md) —
   how a request becomes a persisted, audited clinical fact.
5. [devops/local-development.md](devops/local-development.md) — how to run it.

## Maintenance rule

Documentation is part of the implementation, not an afterthought (root
[CLAUDE.md](../CLAUDE.md), "Documentation Is Part Of The Code"). Every
meaningful change updates [changes/CHANGELOG.md](changes/CHANGELOG.md) and the
specific category docs it affects. This index and the flat `docs/*.md` files
must never describe behavior the repository does not actually implement.
