---
description: Full pre-release gate for Eir EHR across build, tests, migrations, security, privacy, clinical safety, audit, interoperability, observability and docs.
---

Run a release-readiness check for Eir EHR per [CLAUDE.md](../../CLAUDE.md) §95, using the architect, security, privacy, clinical-safety, clinical-governance, integration, fhir, hl7, testing, reliability, audit, devops, and code-review agents as needed.

Check and report on each, citing evidence (a command actually run, a file actually read) rather than assumption:

- **Build**: does `npm ci` and a clean `npm run check` succeed?
- **Tests**: full suite per `/test-all` — unit, contract, Postgres-backed, e2e, `npm audit`.
- **Migrations**: any pending PostgreSQL migration reviewed for the expand→migrate→verify→switch→contract pattern; rollback path documented.
- **Configuration**: no secret committed; every `eir.*.config.json` profile still matches its documented intent (legacy dev / demo / staging / clinic example) — a clinic deployment must not accidentally point at `eir.config.json`.
- **Security**: authorization/session/tenant-isolation regressions per the security agent.
- **Privacy**: PHI exposure regressions per the privacy agent.
- **Clinical safety**: patient-safety gate (CLAUDE.md §62) for every HIGH/CRITICAL-risk change since the last release.
- **Audit**: every new sensitive action has a corresponding audit event.
- **Interoperability**: FHIR export claims match `docs/FHIR.md`'s actual implemented/not-implemented split; no HL7 capability is implied unless genuinely built.
- **Observability**: are failure/backlog signals present for anything new and async?
- **Rollback/recovery**: is a backup/restore drill still valid against current schema (docs/RECOVERY.md)?
- **Documentation**: does `docs/*.md` still describe actual behavior, not aspirational behavior (CLAUDE.md §111)?

Use precise verification language throughout (CLAUDE.md §112) — "implemented and unit-tested" vs. "implemented but not end-to-end verified" vs. "not implemented." Never write "production-ready," "FHIR compliant," "secure," or "clinically validated" without cited evidence.

Final output: a punch list of blockers vs. non-blocking follow-ups, not a single pass/fail verdict.
