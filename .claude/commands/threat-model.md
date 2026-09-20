---
description: Threat-model a specific Eir EHR feature — assets, actors, trust boundaries, entry points, threats, mitigations, residual risk.
argument-hint: <feature or component to threat-model>
---

Threat-model the following Eir EHR feature/component per [CLAUDE.md](../../CLAUDE.md) §20 and §93: **$ARGUMENTS**

1. **Assets** — what patient/clinical/credential data or capability does this feature touch or expose?
2. **Actors** — clinician, patient/proxy, administrator, integration/machine principal, unauthenticated caller, privileged database operator (docs/ARCHITECTURE.md's trust model explicitly does not protect against the last one).
3. **Trust boundaries** — where does data cross from browser → API, API → plugin, plugin → database, Eir → external system (lab connector, OIDC IdP, AI provider)? Server plugins are trusted in-process code, not a sandbox — name where that assumption matters here.
4. **Entry points** — every new/changed route, worker cycle, webhook-style receiver (e.g. `/integrations/:connectorId/results`), or UI action.
5. **Threats** — use STRIDE where useful: Spoofing (forged actor/role), Tampering (record/version tampering, audit chain rewrite by a DB admin), Repudiation (missing audit), Information Disclosure (PHI leak), Denial of Service (unbounded query, worker starvation), Elevation of Privilege (IDOR, permission bypass).
6. **Controls already in place** — cite the actual existing control (e.g. "server-resolved actor, client role headers ignored"; "RLS + tenant scoping"; "expected-version optimistic locking"; "hash-chain audit, not externally anchored").
7. **Residual risk** — what remains true even after controls (e.g. "a privileged DB operator can still rewrite the audit chain"; "machine credential compromise scoped to one connector, but still enables receipt spoofing until rotated").
8. **Tests to add** for the important threats identified — name the specific `tests/*.test.ts` file each belongs in.
9. **Unresolved risks** — report explicitly rather than silently accepting them.

Ground every threat and control in the actual repository (grep for the real check), not in generic threat-modeling boilerplate.
