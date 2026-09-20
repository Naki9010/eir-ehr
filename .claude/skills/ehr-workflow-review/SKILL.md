---
name: ehr-workflow-review
description: Trace a complete Eir EHR clinical workflow end to end (UI → API → domain plugin → storage → audit → FHIR/integration boundary) before declaring a feature done. Use when a change spans multiple layers, or before finishing any feature that touches patients, encounters, notes, medications, results, or tasks.
---

# EHR Workflow Review

Eir is a plugin-composed system (see [CLAUDE.md](../../../CLAUDE.md) and `.claude/rules/architecture.md`). A feature that looks "done" at one layer is often incomplete across the full workflow. Trace it explicitly:

## 1. Identify every layer this workflow actually touches

- UI: `apps/web/**` — form/screen, chart renderer if the data is chart-visible.
- API: the Fastify route in `apps/app.ts` and its request schema.
- Domain: the owning plugin under `plugins/*.ts` and its contract in `packages/contracts.ts`.
- Storage: does this add/change persisted shape in `plugins/storage-sqlite.ts` and `plugins/storage-postgres.ts` (both — never just one)?
- Audit: is a new sensitive action introduced that needs an audit write in the same transaction?
- Authorization: what permission from `packages/workforce.ts` gates this?
- Interop: does this entity need a FHIR mapping (`plugins/fhir-r4.ts`) or appear in an integration payload?
- Worker: does this need background processing (a new or existing `apps/*-worker.ts`)?
- Tests: which `tests/*.test.ts` and `tests/*.e2e.ts` cover it?
- Docs: which `docs/*.md` describes this area and needs updating?

## 2. For each touched layer, confirm the actual state transition, not just "it returns 200"

Walk the real lifecycle: draft → signed/finalized → amendment; received → matched → applied → reviewed → action complete; enrolled → evaluated → alert → acknowledged → resolved. A layer that returns success without moving the record through its real states is not done — see `.claude/rules/clinical-safety.md`.

## 3. Confirm the negative paths, not just the happy path

Wrong patient, wrong org/unit, stale version, duplicate request, missing permission — per `.claude/rules/testing.md`'s negative-case matrix.

## 4. Report using CLAUDE.md §114's format

State exactly which layers were touched, which were verified by an actual test run, and which (if any) were deliberately left out of scope with a reason.
