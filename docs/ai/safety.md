# AI Safety

Primary source: CLAUDE.md §45, [docs/ARCHITECTURE.md](../ARCHITECTURE.md),
[docs/OPERATIONS.md](../OPERATIONS.md).

## What AI is structurally prevented from doing

- **Cannot sign a note.** Acceptance produces a draft; signing is a
  separate, later, human-only action (see [human-review.md](human-review.md)).
- **Cannot prescribe or alter medications.** The medication module has no
  AI-writable path — see [../clinical/medications.md](../clinical/medications.md).
- **Cannot acknowledge a critical lab result or close follow-up work.**
  These require the assigned clinician's explicit action (see
  [../clinical/laboratory-results.md](../clinical/laboratory-results.md)).
- **Cannot fabricate a citation that survives review** — the source-
  quotation validator rejects invented references (see [evidence.md](evidence.md)).
- **Cannot see more than the bounded, authorized encounter context** — no
  cross-patient or full-chart access (see [../privacy/data-flows.md](../privacy/data-flows.md)).
- **Cannot become a single point of failure** — core clinical workflows
  (manual note writing, medication/lab workflows) function with AI
  disabled, unavailable, or misconfigured.

## What AI safety here does NOT mean

None of the above proves the AI's clinical *reasoning* is correct — only
that its citations are real and its output cannot silently reach an
authoritative record without human action. Model-specific clinical
evaluation, hallucination/omission scoring on a Swedish clinical test set,
and per-model-version release approval remain open work (docs/PLAN.md "AI
evaluations", P1). See [validation.md](validation.md) and
[../clinical/clinical-governance.md](../clinical/clinical-governance.md).

## AI-generated content is distinguishable

Proposals are a distinct entity kind from notes; accepted content becomes a
draft note before signing, at which point it carries the same author
attribution as any human-written note — there is currently no separate
persistent "AI-originated" flag retained on the final signed note beyond
what the proposal/evidence history in the entity's version chain records.
If a deployment needs an explicit, permanent AI-provenance marker on signed
notes, that is not yet implemented and would need its own design.
