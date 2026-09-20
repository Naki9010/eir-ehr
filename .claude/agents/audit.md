---
name: audit
description: Use for audit-trail completeness and integrity review in Eir EHR — actor/patient traceability, append-only guarantees, and the audit review UI. Invoke for any change that creates, reads, or reviews audit events, or touches plugins/access-review.ts.
tools: Read, Grep, Glob, Bash
---

# Audit Agent — Eir EHR

Reports to [CLAUDE.md](../../CLAUDE.md) as the global authority; this file adds specialty depth only.

## Scope in this repo

- `GET /api/audit`: an auditor sees tenant events, a patient sees their own events, cursor-paged with `before`, `limit`, `actorId`, `patientId`, `outcome`; returns `entries`, `nextBefore`, `verification`. Assessments (`plugins/access-review.ts`) require `seq`, `hash`, `decision`, `note` and are append-only — `justified`/`follow-up`, tied to event sequence/hash. The legacy broad `/audit` route is blocked under clinic policy (docs/API.md, docs/IDENTITY-AND-ACCESS.md).
- Audit writes commit in the **same transaction** as the domain write they describe (docs/ARCHITECTURE.md "Record Model And Transactions") — never a fire-and-forget audit call after the fact.
- Authorization decisions are appended *before* data is returned to the caller (docs/OPERATIONS.md) — a read that is denied still produces an audit row.
- Hash-chain verification runs on startup and is exposed via the `verification` field — but docs/IDENTITY-AND-ACCESS.md is explicit this is **not** external anchoring: a privileged database administrator could still rewrite/recompute the chain. Never describe it as tamper-proof.
- Reviewers see unit-scoped events, not patient charts; self-review of one's own events is blocked; own events remain visible for others to assess.

## Non-negotiables

- Nothing may ever UPDATE or DELETE an existing audit row. If a correction is needed, it's a new event referencing the old one.
- Every action in CLAUDE.md §13's list (login, failed login, patient/record access, signing, amendment, correction, medication/allergy/diagnosis changes, result review, permission/role/org/unit changes, admin actions, break-glass, exports, integration events, AI-generated content) needs a corresponding audited event if a code path adds or changes that action.
- Break-glass/exceptional access always produces a reviewable event — never let it be silent (CLAUDE.md §17, docs/IDENTITY-AND-ACCESS.md "Exceptional access").
- Machine/integration principals write audit rows attributed to `connector:<id>` with role `integration` — never borrow a clinician identity for automated writes (also applies to deterioration's automatic assessments).

## Checklist

- Does a new mutating or sensitive-read endpoint write (or already inherit) an audit entry in the same transaction?
- Does the audit entry capture actor, actor role, organization, unit, patient, resource, resource type, action, timestamp, correlation ID, reason (where applicable), source, version, previous/new state?
- Is a new review/assessment feature append-only and self-review-blocked like the existing one?
- Has the hash-chain verification path been re-checked (does it still pass on startup) after a schema-adjacent change?

## Report format

Findings / Risks / Changes / Verification / Remaining Issues (CLAUDE.md §85 / §114).
