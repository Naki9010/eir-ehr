---
name: provider-replacement-contract
description: The required behavioral contract test checklist for replacing any Eir EHR provider (storage, identity, AI, country pack, chart renderer, risk engine). Use before calling a provider swap done.
---

# Provider Replacement Contract

From `docs/PLUGINS.md`, this is the standing bar PLAN.md's Open Governance section also invokes: *"New UI renderers and model providers should prove replacement through the same contract suite before a release is labelled compatible."*

## The base contract suite — required for every provider replacement

1. **Happy-path contract behavior** — the interface's documented behavior works end to end.
2. **Wrong-tenant denial** — a request scoped to another tenant/organization is rejected, not silently served.
3. **Missing/expired grants** — an unauthorized or expired caller is rejected.
4. **Record revisions** — optimistic-concurrency / expected-version behavior is correct under the new provider.
5. **Rollback on failure** — a failed operation leaves no partial state (verify inside a `Store.transaction` if the provider participates in one).
6. **Start/stop cleanup** — `onDispose` releases everything acquired; no leaked connections/timers/listeners survive `runtime.stop()`.
7. **Missing dependencies** — the plugin fails clearly at startup if a `requires` service isn't available.

## Additional tests by provider category

- **AI/model providers** (replacing `plugins/ai-extractive.ts` / `ai-ollama.ts`, or `risk-vitals.ts` / `risk-http.ts`): timeouts, output-size limits, invalid-source rejection, stale-context handling, prompt-injection resistance, and a clinician-rated output evaluation kept separate from the mechanical tests above.
- **UI/chart renderers** (replacing or adding to `apps/web/renderers/*.js`): keyboard navigation, mobile layout, patient-context visibility, and every explicit UI state (loading/empty/error/success/unsaved/finalized).
- **Storage providers** (`plugins/storage-*.ts`): the full `Store` interface (`get`, `list`, `searchEntities` if the profile needs it, `insert`, `revise`, `audit`, `history`, `verifyAudit`) plus concurrent-write and restart-persistence tests.
- **Identity/access providers**: every negative case in `.claude/rules/security.md`'s test list (unauthenticated, wrong role, wrong org, wrong unit, wrong patient, expired session, manipulated IDs).
- **Country packs**: identifier validation (checksum/format) specific to the target country, plus the same kernel contract tests the existing pack passes (PLAN.md M6: "same kernel contract tests pass").

## Before calling it done

- Does `npm run plugins` show a clean resolved graph with the new provider in place of the old one?
- Do the existing tests for the *replaced* provider's consumers still pass unmodified (proving the contract, not the implementation, is what's tested)?
- Is the change documented per CONTRIBUTING.md (trust/dependencies/cleanup/license/failure modes) and per `docs/PLUGINS.md`'s "Replace A Provider" pattern?
