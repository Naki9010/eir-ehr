---
description: Implement a complete cross-layer feature in Eir EHR, from architecture through documentation, using the specialist agents.
argument-hint: <feature description>
---

Implement the following for Eir EHR, following [CLAUDE.md](../../CLAUDE.md) §63 (Feature Implementation Pipeline) and §87:

**Request:** $ARGUMENTS

Work through, using the matching specialist agent in `.claude/agents/` for each stage and skipping only stages that are genuinely not applicable:

1. **Discover** — inspect the repository (`packages/contracts.ts`, `packages/runtime.ts`, relevant `plugins/*.ts`, relevant `docs/*.md`) before assuming anything about current behavior.
2. **Architect** — identify affected plugins/services/layers; confirm this extends the existing plugin/provider pattern rather than adding a parallel mechanism.
3. **Clinical Safety** — run the patient-safety checklist (CLAUDE.md §62) for anything touching patients, encounters, notes, medications, allergies, diagnoses, results, or tasks.
4. **Security** — authorization, tenant/patient isolation, session/credential handling for any new/changed endpoint.
5. **Privacy** — PHI exposure in logs, telemetry, exports, browser storage.
6. **Implement backend** — API, domain logic, validation, authorization, transactions per the backend agent's conventions.
7. **Database** — schema/migration if needed, following expand → migrate → verify → switch → contract; concurrency/version handling.
8. **Implement frontend** — UI per the frontend/UX agents' conventions (patient context, states, accessibility).
9. **Integrations/FHIR/HL7** — only if the feature actually touches `plugins/integrations.ts`, `plugins/fhir-r4.ts`, or genuinely needs new external messaging (confirm HL7 doesn't already falsely apply — see the hl7 agent).
10. **Audit** — add/verify audit events for every sensitive action introduced.
11. **Tests** — unit/contract tests and, if user-facing, e2e tests; run `npm run check` and relevant `tests/*.e2e.ts`.
12. **Reliability** — failure modes for anything async/queued/worker-based.
13. **Code review** — independent pass per the code-review agent; do not just restate other agents' self-reports.
14. **Documentation** — update the relevant `docs/*.md` if behavior changed.
15. **Final verification** — report using CLAUDE.md §114's format (Implemented / Files Changed / Architecture / Security / Privacy / Clinical Safety / Interoperability / Reliability / Tests / Verification / Remaining Issues).

Do not skip a stage silently — if a stage doesn't apply, say so explicitly in the final report rather than omitting it.
