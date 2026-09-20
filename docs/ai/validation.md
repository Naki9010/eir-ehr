# Validation (Post-Inference)

Primary source: [docs/ARCHITECTURE.md](../ARCHITECTURE.md), [docs/OPERATIONS.md](../OPERATIONS.md).

## Stale-context check

Acceptance (`AIReview.review` with `decision: "accept"`) rechecks
permission, encounter status, **and all evidence revisions** at the moment
of review — not just at proposal time. If any cited record has been revised
since the proposal was generated, the proposal is stale and cannot be
silently accepted against outdated evidence (docs/OPERATIONS.md "Changed
permissions/context during inference" row). The medication/results release
extended this: a changed medication/lab source invalidates a pending
proposal (docs/MEDICATIONS-AND-RESULTS.md).

## Replay protection

An accepted or rejected proposal cannot be replayed — a second review call
against the same proposal ID/version is rejected, matching the same
optimistic-concurrency discipline used everywhere else in the record model
(see [../database/concurrency.md](../database/concurrency.md)).

## What remains a gap

Quotation-existence validation is not clinical-correctness validation (see
[evidence.md](evidence.md)). No Swedish clinical evaluation corpus, omission/
hallucination scoring, or per-model-release approval process exists yet —
tracked as P1 in [docs/PLAN.md](../PLAN.md) ("AI evaluations").
