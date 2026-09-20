# AI-Assisted Documentation

Primary source: [docs/ARCHITECTURE.md](../ARCHITECTURE.md), [docs/PLUGINS.md](../PLUGINS.md).

## Purpose

Generate a reviewable, evidence-cited draft note proposal from authorized
encounter context — never an automatically signed clinical action.

## User Workflow

Clinician requests a proposal for the open encounter → reviews the proposed
text and its citations → accepts (optionally edited) into a new **draft**
note, or rejects → the draft goes through the ordinary sign workflow
separately.

## Architecture

`AIReview` (propose/review) + `AIProvider` (extractive or Ollama), with
`ai-review.ts` owning evidence validation and the review state machine
regardless of provider. See [../ai/architecture.md](../ai/architecture.md).

## Data Model

`proposal` entity kind: text, citations (`Evidence[]`), model ID, mode
(`extractive`|`model`).

## API

`POST /patients/:id/ai` (`{encounterId}`), `POST /proposals/:id/review`
(`{version, decision, text?}`).

## Authorization

`ai.use` to propose/review; acceptance additionally requires `record.write`.

## Security

The Ollama adapter rejects non-loopback endpoints and redirects; no
signing/prescribing/shell/SQL tool is exposed to any provider.

## Privacy

Evidence is bounded to the authorized, open encounter — never the full
chart or cross-patient data.

## Clinical Safety

Acceptance produces a **draft**, not a signed note; a second, separate
clinician action (signing) is required. Post-inference rechecking rejects
stale evidence. See [../ai/safety.md](../ai/safety.md).

## Audit

Proposal generation and review decisions are audited like any clinical
mutation.

## Interoperability

Not applicable — proposals are pre-clinical-record artifacts, never
directly exported.

## Error Handling

An accepted/rejected proposal cannot be replayed; changed source context
requires regeneration rather than accepting stale evidence.

## Concurrency

Review uses expected-version optimistic concurrency like any other entity.

## Testing

`tests/plugins-ai.test.ts`; `npm run smoke:model` for real-Ollama transport
verification.

## Known Limitations

No Swedish clinical evaluation corpus, no hallucination/omission scoring,
no per-model-release approval process. Quotation-existence validation does
not prove clinical-reasoning correctness.

## Future Improvements

De-identified/synthetic Swedish test set with clinician scoring; per-model
release approval — tracked as P1 "AI evaluations" in
[docs/PLAN.md](../PLAN.md). Ambient documentation, coding candidates,
referral preparation, and result-inbox prioritization are named as future
uses of this same proposal/review mechanism (docs/ARCHITECTURE.md).
