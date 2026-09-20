---
name: threat-modeling
description: Threat-model a specific Eir EHR feature or component using assets/actors/trust-boundaries/entry-points/threats/controls/residual-risk. Use for new endpoints, new integrations, or anything crossing a trust boundary (browser↔API, plugin↔database, Eir↔external system).
---

# Threat Modeling

See `.claude/commands/threat-model.md` for the full walkthrough this skill summarizes, and `.claude/rules/security.md` for the concrete controls already in place.

## Steps

1. **Assets** — what patient/clinical data or credential/capability does this touch?
2. **Actors** — clinician, patient/proxy, administrator, integration/machine principal, unauthenticated caller, and (always worth naming explicitly) a privileged database operator, since Eir's own trust model does not protect against that actor.
3. **Trust boundaries** — browser→API, API→plugin, plugin→database, Eir→external system (lab connector, OIDC IdP, AI provider). Remember: server plugins are trusted in-process code, not a sandbox — a "boundary" between two plugins is a programming contract, not a security control.
4. **Entry points** — every new/changed route, worker cycle, or receiver endpoint (e.g. `/integrations/:connectorId/results`).
5. **Threats (STRIDE)** — Spoofing (forged actor/role — already mitigated by server-resolved identity), Tampering (record/version/audit tampering), Repudiation (missing audit), Information Disclosure (PHI leak), Denial of Service (unbounded query, worker starvation), Elevation of Privilege (IDOR, permission bypass).
6. **Existing controls** — cite the actual mechanism (RLS, expected-version checks, hash-chain audit, machine-credential scoping) rather than a generic "authentication is required."
7. **Residual risk** — state plainly what remains true even with controls (e.g. a DB admin can still rewrite the audit chain; a compromised connector credential can spoof receipts for its one connector until rotated).
8. **Tests to add** — name the specific `tests/*.test.ts` file each threat's regression test belongs in.

## Non-negotiable

Ground every threat and control in the actual repository (grep for the real check) — generic threat-modeling boilerplate that doesn't cite a real file or behavior is not useful here and should be treated as incomplete.
