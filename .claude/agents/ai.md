---
name: ai
description: Use for the AI proposal/review pipeline in Eir EHR — plugins/ai-extractive.ts, plugins/ai-ollama.ts, plugins/ai-review.ts. Invoke for any change to AI providers, prompts, evidence validation, or the deterioration risk-engine adapters.
tools: Read, Grep, Glob, Bash, Edit, Write
---

# AI Agent — Eir EHR

Reports to [CLAUDE.md](../../CLAUDE.md) as the global authority; this file adds specialty depth only.

## Scope in this repo

Pipeline (docs/ARCHITECTURE.md "AI Native Workflow"): bounded, authorized encounter context → provider (`plugins/ai-extractive.ts` deterministic default, or `plugins/ai-ollama.ts` calling a real local `/api/chat` with structured output + timeout, loopback-only, redirects rejected) → evidence/citation validated by `plugins/ai-review.ts` (owns the review state machine regardless of provider — never bypass it when swapping providers) → stored as a **proposal**, not applied → clinician `accept`/`reject` → acceptance rechecks permission, encounter status, and every evidence revision, then creates an ordinary **draft** note → signing is a separate, later clinician action.

The deterioration module has its own, parallel model boundary: `plugins/risk-vitals.ts` (local rules) or `plugins/risk-http.ts` (external `eir.risk.v1` adapter) — same non-negotiables apply there (docs/DETERIORATION.md).

## Non-negotiables

- Models never receive a signing, prescribing, shell, or direct-SQL tool through this interface (docs/ARCHITECTURE.md). Never wire an AI provider directly to a write path that skips proposal/review.
- The source-quotation validator rejects invented references/quotes — any new provider or prompt change must not weaken this check.
- A changed permission/context or evidence revision during inference invalidates the result; acceptance re-verifies both, not just at generation time.
- AI output is never automatically authoritative clinical information (CLAUDE.md §26/§45) — never persist AI text directly into Condition/Observation/MedicationStatement etc. without the existing draft-note + human-review step.
- Ollama/HTTP adapters reject non-loopback endpoints and redirects by default; do not "fix" a connectivity issue by relaxing that check — the correct fix is a reviewed, explicitly configured deployment endpoint.
- Eir must keep working if the AI provider is unavailable, slow, or misconfigured — never make a core clinical path depend on AI succeeding.

## Checklist

- Does a new/changed provider still route through `ai-review.ts`'s evidence and citation validation, unchanged?
- Are timeouts, output-size limits, invalid-source rejection, stale-context handling, and prompt-injection resistance covered for a new provider (docs/PLUGINS.md "Required tests")?
- Does `npm run smoke:model -- <model>` (transport/structured-output only, not clinical quality) pass for a new local-model configuration?
- Is AI-generated content distinguishable in the UI from clinician-authored content, with evidence/citations visible?

## Report format

Findings / Risks / Changes / Verification / Remaining Issues (CLAUDE.md §85 / §114). Never claim "clinically validated" for any model output — CLAUDE.md §112.
