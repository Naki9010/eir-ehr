# Clinical Governance

Primary source: [.claude/rules/clinical-governance.md](../../.claude/rules/clinical-governance.md).
See also [.claude/agents/clinical-governance.md](../../.claude/agents/clinical-governance.md).

## What Eir owns vs. what a clinical/organizational owner must own

| Eir owns (technical capability) | A clinical/organizational owner must own (policy) |
| --- | --- |
| The terminology *provider interface* and ICD-10-SE import pipeline | Which coding system a clinic uses, and any local coding convention beyond the official release |
| The deterioration engine *interface* (`RiskEngine`, `eir.risk.v1`) and its bundled development-fixture thresholds | The actual clinical threshold, trend window, and escalation deadline for a real deployment |
| The follow-up *engine* (deadline computation, notification delivery, worker cadence) | The actual policy provider's deadlines (`plugins/follow-up-policy.ts` config) |
| The permission *model* (24 granular actions in `packages/contracts.ts`) | Which role gets which permission — a duties-based needs/risk analysis the organization must approve (citing IMY guidance) |
| The modules *activation mechanism* (`modules.manage`, reason, expected revision) | Whether a given unit should actually turn on deterioration/coordination, and a deployment-level `canEnable` decision |

## Rule: never fill a policy gap with a plausible-sounding default

If a task requires a clinical threshold, a permission-to-role mapping, an
escalation SLA, or a terminology decision not already in the repository's
config or docs, do not invent one — not even a "reasonable" one. Name the
missing decision, implement the configuration surface so it *can* be
supplied, and leave the value unset or clearly marked as a placeholder
requiring review, matching how `risk-vitals.ts` already labels its defaults
as "development fixtures."

## Precision about claims

Never describe Eir as certified, clinically validated, medically approved,
production-ready, nationally integrated, or fully interoperable without
citing specific evidence. Use the six states from CLAUDE.md §50/§112 —
Implemented, Configured, Connected, Tested, Verified/Validated, Certified —
and don't conflate them. `docs/FHIR.md`'s "Not Implemented Or Not Proven"
section and `docs/DETERIORATION.md`'s "not the CHARTwatch model" are the
existing models to match.

## Swedish/national-service scope

Personnummer/samordningsnummer validation, ICD-10-SE, and FHIR R4 export are
implemented. SITHS, 1177, NPÖ, Nationella läkemedelslistan, EHDS exchange,
and any specific regional/national integration are **not** — see
[docs/SWEDISH-INTEGRATIONS.md](../SWEDISH-INTEGRATIONS.md) and
[docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md) for the actual
state before making any claim in this area.
