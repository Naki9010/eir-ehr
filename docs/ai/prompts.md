# Prompts

## Current state

Prompt construction is internal to each `AIProvider` implementation
(`plugins/ai-extractive.ts`, `plugins/ai-ollama.ts`), not a
centrally-documented prompt template, because the extractive provider does
not prompt an LLM at all, and the Ollama provider's prompt is bound tightly
to its structured-output schema (see [structured-output.md](structured-output.md)).

## What governs prompt content regardless of provider

The `Evidence[]` array passed to `generate()` is the **only** clinical
context a provider receives — bounded to the authorized, open encounter,
not the full chart or other patients' data (see
[../privacy/data-flows.md](../privacy/data-flows.md)). No system prompt in
this codebase grants a model tool access to signing, prescribing, or direct
storage writes (see [safety.md](safety.md)).

## Not yet built

No prompt-injection test suite is documented as passing today — CLAUDE.md
lists prompt injection explicitly as a required AI-provider replacement
test (docs/PLUGINS.md "Required tests for a replacement"), and
[docs/PLAN.md](../PLAN.md) lists "prompt injection" testing under the P1 "AI
evaluations" backlog item — not yet complete.
