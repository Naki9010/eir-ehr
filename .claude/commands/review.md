---
description: Repository-wide review of Eir EHR across architecture, security, privacy, clinical safety, interoperability, reliability, testing, UX and maintainability. Read-only.
argument-hint: [optional focus area or path]
---

Perform a repository-wide review of Eir EHR per [CLAUDE.md](../../CLAUDE.md) §88. **Do not modify code** — this is read-only unless the user explicitly asks for fixes afterward.

Focus: $ARGUMENTS (if empty, review the current diff against `main`, or the whole repository if there is no diff).

Check, using the matching agent for each area:

- **Architecture** (architect agent): plugin/provider boundaries respected, no unjustified new framework/ORM/database.
- **Security** (security agent): authentication, authorization, tenant/patient isolation, IDOR, secrets.
- **Privacy** (privacy agent): PHI exposure in logs, telemetry, storage, exports.
- **Clinical safety** (clinical-safety agent): patient identity, record integrity, finalized-data protection, workflow-completion semantics.
- **Data integrity** (database agent): transactions, concurrency, migrations.
- **Interoperability** (fhir + integration + hl7 agents): FHIR mapping accuracy and claims, integration idempotency/retry, and confirming no false HL7 claims exist.
- **Reliability** (reliability agent): worker/queue failure modes.
- **Testing** (testing agent): coverage gaps against the negative-case matrix in CLAUDE.md §56/§57.
- **UX** (ux agent): patient-context clarity, accessibility, state handling.
- **Performance**: N+1 queries, unbounded datasets, missing pagination.
- **Maintainability**: dead code, duplicated logic that should be centralized.

Report each finding with: file/location, what's wrong, why it matters (which priority in CLAUDE.md §2 it threatens), and a suggested fix — but do not apply the fix unless asked.
