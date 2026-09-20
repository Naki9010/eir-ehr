# Clinical Documentation

Primary sources: [docs/ARCHITECTURE.md](../ARCHITECTURE.md) ("Record Model And
Transactions", "UX Contract"), [docs/MEDICATIONS-AND-RESULTS.md](../MEDICATIONS-AND-RESULTS.md),
[docs/DETERIORATION.md](../DETERIORATION.md), [docs/FOLLOW-UP.md](../FOLLOW-UP.md),
[docs/CARE-TEAM.md](../CARE-TEAM.md), [docs/PLAN.md](../PLAN.md). See also
[.claude/rules/clinical-safety.md](../../.claude/rules/clinical-safety.md) and
[.claude/rules/clinical-governance.md](../../.claude/rules/clinical-governance.md).

## Contents

- [patient-safety.md](patient-safety.md) — the patient-safety gate and how the record model enforces it
- [patient-identity.md](patient-identity.md) — identifiers, protected identity, the (unimplemented) merge/split boundary
- [clinical-workflows.md](clinical-workflows.md) — the registration → encounter → document → follow-up loop
- [clinical-notes.md](clinical-notes.md) — the note lifecycle state machine
- [medications.md](medications.md) — medication statements and reconciliation
- [allergies.md](allergies.md) — allergy recording and correction
- [diagnoses.md](diagnoses.md) — ICD-10-SE coding
- [laboratory-results.md](laboratory-results.md) — order → result → review lifecycle
- [clinical-review.md](clinical-review.md) — audit/access review surface for clinical actions
- [clinical-governance.md](clinical-governance.md) — what Eir owns vs. what a clinical/organizational owner must own
- [safety-decisions/](safety-decisions/README.md) — clinical-safety-scoped ADRs

## The one rule that governs everything in this folder

**Never invent clinical rules, thresholds, or policy.** Where a real clinical
decision is required (a deterioration threshold, an escalation SLA, a
permission-to-role mapping), Eir implements the *technical structure* to
hold that decision and leaves the value itself either as a clearly labelled
development fixture or unset, pending an accountable clinical/organizational
owner. See [clinical-governance.md](clinical-governance.md).
