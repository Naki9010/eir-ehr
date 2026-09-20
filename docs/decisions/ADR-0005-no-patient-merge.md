# ADR-0005 — Do Not Implement Patient Merge/Split Until A Dedicated Design Exists

## Status
Accepted

## Date
2026-09-19 (documented explicitly in [.claude/rules/clinical-safety.md](../../.claude/rules/clinical-safety.md))

## Context

Duplicate-patient records are a real, expected operational problem in any
EHR (e.g. a patient registered twice under slightly different identifier
input). Teams under pressure to "fix" a duplicate-patient complaint quickly
are tempted to improvise a quick merge — reassigning one patient's records
to another's ID.

## Decision

**Do not implement patient merge/unmerge as a quick fix.** Treat it
explicitly as CRITICAL-risk new work (CLAUDE.md §60) requiring its own
controlled, permission-protected, reversible-where-possible, fully-audited,
explicitly-reviewed design (CLAUDE.md §11) — not an ad hoc script or a
direct-store reassignment.

## Alternatives Considered

- A quick administrative "reassign patientId" script for support use —
  rejected outright: it would silently rewrite clinical history's patient
  attribution with no audit trail proportional to the risk, directly
  violating CLAUDE.md §8 ("Never silently... merge patients").
- Soft "linking" of duplicate records without reassignment — not rejected,
  but also not designed or built; this remains a legitimate future
  direction that still requires its own dedicated design and review before
  implementation.

## Why

Patient identity is the single highest-consequence correctness property in
an EHR — a silent or under-audited merge could misattribute one patient's
clinical history to another, a wrong-patient risk with no acceptable quick
fix (see [../clinical/patient-safety.md](../clinical/patient-safety.md)).

## Consequences

**Positive**: no unreviewed, under-audited merge capability exists to be
misused or to fail silently.

**Negative**: duplicate-patient records currently have no built-in
resolution path; an operator must currently handle this manually and
outside the system (or wait for the dedicated feature to be designed and
built) — a named, real limitation (see
[../clinical/patient-identity.md](../clinical/patient-identity.md) "What
does NOT exist").

## Security Impact

Prevents a large, unreviewed authorization/audit surface (cross-patient
record reassignment) from existing without dedicated review.

## Privacy Impact

Prevents an unreviewed mechanism for redirecting one patient's data under
another patient's identity.

## Clinical Impact

Direct: this decision *is* a clinical-safety decision. See CLAUDE.md §11.

## Interoperability Impact

None currently — no merge capability exists to interact with FHIR export
or integrations.

## Reliability Impact

None directly.
