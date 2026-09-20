---
name: clinical-governance-review
description: Check whether an Eir EHR change invents clinical policy (a threshold, permission mapping, escalation rule, terminology decision) that should instead be an explicit, owner-approved configuration value. Use whenever a request implies a clinical rule or organizational policy that isn't already in the repo.
---

# Clinical Governance Review

See `.claude/rules/clinical-governance.md` for the full detail this skill draws from.

## The test to apply

For any number, rule, or mapping the change is about to add, ask: **is this a technical capability, or a clinical/organizational policy decision?**

- Technical capability (Eir's to build): a provider interface, a configuration surface, an engine that can be pointed at a supplied threshold/policy.
- Policy decision (a clinical/organizational owner's to make): the actual threshold value, the actual role-to-permission mapping, the actual escalation SLA, the actual terminology/coding convention beyond the official release.

If the change is about to hardcode a policy decision as if it were a technical default, stop.

## What to do instead

1. Name the specific missing decision (e.g. "no clinic has approved a respiratory-rate alert threshold for this deployment").
2. Confirm it isn't already answered somewhere in `docs/*.md` or an existing `eir.*.config.json` profile — check before assuming it's missing.
3. Implement the configuration surface (a typed config field, a provider interface) so the decision can be supplied later.
4. Leave the value itself unset, or clearly labeled as a development fixture (matching `plugins/risk-vitals.ts`'s existing convention) — never a plausible-sounding invented default presented as authoritative.
5. Report the unresolved policy decision explicitly rather than letting it disappear into a merged PR.

## Claims discipline

Check any new documentation or code comment against CLAUDE.md §50's six states (Implemented/Configured/Connected/Tested/Validated/Certified) and against docs/SWEDISH-INTEGRATIONS.md / docs/REGULATORY-ALIGNMENT.md for what's actually true about national-service integration today.
