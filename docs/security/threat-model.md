# Threat Model (System-Level)

Primary source: [docs/OPERATIONS.md](../OPERATIONS.md) ("Threats And Enforced
Controls"). For a feature-specific threat model, use the `/threat-model`
command or [.claude/skills/threat-modeling/SKILL.md](../../.claude/skills/threat-modeling/SKILL.md).

| Threat | Current control | Remaining work |
| --- | --- | --- |
| Forged role/tenant headers | Authentication resolves server-side actor; client role headers ignored | Strong eID and verified professional attributes |
| Wrong-patient or wrong-tenant writes | Tenant query scoping, PostgreSQL role-bound RLS, encounter ownership and active-care checks | Complete protected-identity lifecycle and country-specific rights policy |
| Lost update or altered signed note | Expected versions, immutable note DB triggers, amendments, real PostgreSQL concurrency tests | Production-scale load/soak tests and deployed failover exercises |
| Missing access audit | Audit before disclosure; write audit in same transaction | External anchoring, review queue, archival retention |
| Model fabricated source | Exact record/version quotes checked; proposals reviewed | Clinical correctness evaluation — quotations alone don't prove correctness |
| Changed permissions/context during AI inference | Recheck access after inference; compare evidence again on acceptance | Cancellable jobs, cross-process inference orchestration |
| Dependency/plugin compromise | Operator-selected source, lockfile, `npm audit` in CI | Signed distribution, SBOM, isolated untrusted-extension runtime |
| Browser data disclosure | Escaping, no bearer token in browser storage, no third-party assets | Dedicated security review, CSP regression, assistive-technology tests |

## Assets / actors / trust boundaries (summary)

- **Assets**: patient records, audit chain, session/bearer tokens, workforce
  assignment data, backup encryption keys, lab/notification connector
  credentials.
- **Actors**: clinician, patient, proxy, auditor, administrator, and the
  `integration` machine role (`Actor.role` in `packages/contracts.ts`).
- **Trust boundaries**: browser ↔ API (untrusted until authenticated),
  API ↔ plugin (trusted, in-process), plugin ↔ database (RLS-enforced for
  PostgreSQL), Eir ↔ external transport (lab, notification, risk-model
  HTTP — untrusted network, authenticated endpoints).

## Explicit residual risk

The current patient restriction is intentionally coarse — it is not
Sweden's full unit/provider-specific record-block model. No emergency
override is silently granted. See [../clinical/patient-identity.md](../clinical/patient-identity.md).
