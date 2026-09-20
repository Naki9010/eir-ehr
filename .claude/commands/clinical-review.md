---
description: Focused clinical-safety and clinical-governance review of a change in Eir EHR — patient identity, workflow correctness, data integrity, audit.
argument-hint: [optional path or feature name]
---

Run a clinical review of $ARGUMENTS (default: current diff against `main`) using the **clinical-safety**, **clinical-governance**, **audit**, and **testing** agents (CLAUDE.md §89).

Focus specifically on:

- **Patient identity**: is the patient guaranteed correct and server-resolved everywhere touched?
- **Clinical context**: is the correct encounter/episode used; does it respect the one-open-encounter-per-patient rule?
- **Workflow correctness**: does the change respect the real lifecycle states already in the repo (note draft/signed/amended; result received vs. reviewed vs. acted-on; deterioration alert acknowledge/reassess/resolve; follow-up open/closed with safe-to-open disposition default)?
- **Data integrity**: are finalized/signed records protected; do corrections create new versions with `entered-in-error` on the old one instead of in-place edits?
- **Medications, results, tasks**: any change here gets the full CLAUDE.md §57 clinical-safety test matrix.
- **Audit**: is every sensitive action in the change actually audited, in the same transaction as the domain write?
- **Governance boundary**: does the change invent any clinical rule, threshold, or policy that should instead be an explicit, owner-approved configuration value (clinical-governance agent)?

Report using CLAUDE.md §85 format (Findings / Risks / Changes / Verification / Remaining Issues), with an explicit answer to each of the 15 questions in CLAUDE.md §62 (Patient Safety Gate) for anything at HIGH or CRITICAL risk per §60.
