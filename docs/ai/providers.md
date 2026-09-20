# AI Providers

Primary source: [docs/PLUGINS.md](../PLUGINS.md) ("Replace A Provider").

## `plugins/ai-extractive.ts` (default)

Deterministically extracts source text — no language model, no network
call. Every shipped profile (`eir.config.json`, `eir.demo.config.json`,
`eir.clinic.config.example.json`) defaults to this provider. It is
"visibly identified as not using a language model" (README.md).

## `plugins/ai-ollama.ts` (alternative)

Calls a real local Ollama `/api/chat` endpoint with structured output and a
timeout. The adapter **rejects non-loopback endpoints and redirects** — it
does not silently send data to an external provider even if misconfigured.
`npm run smoke:model -- <installed-model>` exercises the real adapter with
synthetic evidence and validates returned references; this verifies
transport/structured-output correctness only, **not clinical quality**. A
live smoke test with `qwen3.5:4b` succeeded during development (docs/PLUGINS.md)
— this is a transport-verification result, not a clinical evaluation
result.

## Replacing the provider

```json
{ "module": "./plugins/ai-ollama.ts", "config": { "endpoint": "http://127.0.0.1:11434", "model": "your-installed-model" } }
```

Keep `ai-review.ts` active regardless of provider — it owns evidence
validation and the review state machine (see [architecture.md](architecture.md)).
Browser tests run against the extractive adapter; live-model performance is
a separate, not-yet-completed evaluation gate (docs/PLAN.md "AI
evaluations").

## Availability requirement

Eir must remain functional if AI is unavailable, slow, rate-limited,
misconfigured, or disabled (CLAUDE.md §47) — AI proposal/review is an
additive workflow on top of ordinary manual note drafting, never a
prerequisite for it.
