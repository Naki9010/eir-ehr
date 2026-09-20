# AI Documentation

Primary source: [docs/ARCHITECTURE.md](../ARCHITECTURE.md) ("AI Native
Workflow"), [docs/PLUGINS.md](../PLUGINS.md) ("Replace A Provider"),
CLAUDE.md §45–§47.

## Contents

- [architecture.md](architecture.md) — the proposal/review pipeline
- [providers.md](providers.md) — the extractive and Ollama providers
- [prompts.md](prompts.md) — what prompting exists (minimal, by design)
- [structured-output.md](structured-output.md) — the `ProposalOutput` contract
- [evidence.md](evidence.md) — citation/evidence validation
- [validation.md](validation.md) — post-inference rechecking
- [human-review.md](human-review.md) — the accept/reject workflow
- [safety.md](safety.md) — what AI is prevented from doing

## The one sentence that governs this folder

AI is assistive and is never automatically the source of truth for clinical
data — accepted output becomes an ordinary **draft** note that a clinician
must separately review and sign; nothing here allows AI output to reach a
finalized clinical record without a human action in between.
