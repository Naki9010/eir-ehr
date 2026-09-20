# AI Architecture

Primary source: [docs/ARCHITECTURE.md](../ARCHITECTURE.md) ("AI Native
Workflow"), CLAUDE.md §46 ("AI Pipeline").

## Pipeline

```
Patient Context → Bounded Input → AI Provider → Structured Output
  → Evidence Validation → Citation Validation → Stale Context Check
  → Human Review → Optional Persistence
```

The common clinical command API is used by both humans and AI review — the
AI path does not have privileged write access the human path lacks.
`AIReview.propose(actor, patientId, encounterId)` builds an evidence
snapshot (record IDs and versions) from the authorized, open encounter,
sends a bounded copy to the configured `AIProvider`, and stores the
returned text + citations + evidence as a `proposal` entity — not as a
note.

## Post-inference rechecking

`AIReview.review(actor, id, version, decision, text?)` re-verifies
permission, encounter status, and **all** evidence revisions at acceptance
time — not just at proposal time. If the underlying record context changed
between proposal and review, acceptance is rejected rather than accepting
possibly-stale evidence (see [validation.md](validation.md)). Acceptance
creates an ordinary **draft** note; signing remains a separate, later
clinician action (see [../clinical/clinical-notes.md](../clinical/clinical-notes.md)).

## Provider abstraction

`AIProvider` (`packages/contracts.ts`): `{id, generate(evidence):
Promise<ProposalOutput>}`. `ai-review.ts` — not the provider — owns
evidence validation and the review state machine regardless of which
provider is configured (see [providers.md](providers.md)). This is what
makes providers swappable via a profile edit without touching the safety
logic.

## What AI never receives

No signing, prescribing, shell, or direct-SQL tool is exposed through this
interface. Clinical text sent to the model is treated as untrusted input —
a model can still produce misleading text with technically-real citations;
human review and model-specific evaluation remain necessary (see
[safety.md](safety.md)).
