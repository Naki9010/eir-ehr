---
name: patient-safety-review
description: Run the Eir EHR patient-safety gate (correct patient, correct clinical context, data integrity, audit, failure handling) on a specific change. Use before finishing any HIGH or CRITICAL risk change per CLAUDE.md §60 — medications, lab results, clinical notes, patient matching/identity, clinical decision support, or emergency access.
---

# Patient Safety Review

Run the 15-question gate from [CLAUDE.md](../../../CLAUDE.md) §62 against the specific change, answering each with a concrete fact from the code — not a general assurance:

1. Is the correct patient guaranteed (server-resolved, never client-supplied)?
2. Is authorization enforced, server-side, for this specific action?
3. Is the clinical context (encounter/episode) correct and current?
4. Can data be lost by this change?
5. Can stale data overwrite newer data (is there a version check)?
6. Can a duplicate event (retry, redelivery) cause a duplicate clinical action?
7. Is finalized/signed data protected from this change?
8. Are corrections traceable (new version + `entered-in-error`, not in-place edit)?
9. Is the action audited, in the same transaction as the write?
10. What happens if the database write fails mid-transaction?
11. What happens if an external system (lab, AI provider, IdP) fails during this workflow?
12. What happens if the request is retried?
13. What happens if two users act on the same patient/record simultaneously?
14. Could the user (clinician) misread the resulting state as "done" when it isn't — see the received/reviewed/acted-on distinction in `.claude/rules/clinical-safety.md`?
15. Can this workflow produce a wrong-patient action under any realistic sequence (tab switch, stale cache, delayed response)?

If any answer is "unclear" or "I'm not sure," that is a finding — investigate the actual code path (`plugins/clinical.ts`, `packages/contracts.ts`'s `Store.transaction`, the relevant domain plugin) before writing the review, rather than asserting it's fine.

Report each answer explicitly; don't summarize as "patient safety looks good" without showing the 15 answers.
