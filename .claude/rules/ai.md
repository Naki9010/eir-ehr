# AI Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md) §45–§47. This file gives the AI agent the detailed shape.

## The pipeline (docs/ARCHITECTURE.md "AI Native Workflow") — do not shortcut any stage

```
Authorized encounter context (bounded copy)
        │
        ▼
AI provider (plugins/ai-extractive.ts default, or plugins/ai-ollama.ts)
        │
        ▼
plugins/ai-review.ts — evidence + source-quotation validation (owns this regardless of provider)
        │
        ▼
Stored as a PROPOSAL (not applied to the record)
        │
        ▼
Clinician reviews: accept | reject
        │  (accept re-checks permission, encounter status, AND every evidence revision)
        ▼
New DRAFT note created
        │
        ▼
Clinician signs (separate, later action)
```

An accepted or rejected proposal cannot be replayed; a proposal whose source context has changed must be regenerated, not force-accepted.

## Providers

`plugins/ai-extractive.ts` deterministically extracts source text — no model call, the safe default. `plugins/ai-ollama.ts` calls a real local Ollama `/api/chat` endpoint with structured output and a timeout; it rejects non-loopback endpoints and redirects by default — this is a deliberate boundary against accidentally sending clinical context to an external service, not a bug to "fix" by relaxing the check. Swapping providers is a profile edit (`eir.config.json`'s `plugins` array); `ai-review.ts` stays active regardless of which provider is selected.

## The deterioration module has a parallel, separate model boundary

`plugins/risk-vitals.ts` (local rules, default) or `plugins/risk-http.ts` (external `eir.risk.v1` HTTP adapter) — same non-negotiables apply: authorization rechecked before and after inference, model responses with unknown evidence references or inconsistent status rejected outright, automatic writes attributed to an integration audit principal (never recorded as if a clinician decided it).

## Non-negotiables

- No signing, prescribing, shell, or direct-SQL tool is ever exposed to a model through this interface.
- The source-quotation validator's job is to reject invented references/quotes — any prompt or provider change must preserve this, not work around it.
- AI output is never persisted directly into an authoritative clinical field (Condition, Observation, MedicationStatement, etc.) — it always goes through the draft-note + human-sign path.
- A changed permission, encounter status, or evidence revision between generation and acceptance invalidates the proposal — re-verify at acceptance time, every time.
- Eir's core clinical workflows must keep working if the AI provider is slow, unavailable, or misconfigured.

## Required tests for any new/changed provider

Timeouts, output-size limits, invalid-source rejection, stale-context handling, prompt-injection resistance, and (docs/PLUGINS.md) a clinician-rated output evaluation separate from the mechanical transport test. `npm run smoke:model -- <model>` verifies transport/structured-output only — it is explicitly not a clinical-quality evaluation, and should never be cited as one.
