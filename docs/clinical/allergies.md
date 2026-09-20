# Allergies

Primary source: [docs/API.md](../API.md), [docs/ARCHITECTURE.md](../ARCHITECTURE.md).

## Recording

`POST /api/patients/:id/records/:kind` with `kind=allergy`:
`{substance, reaction, criticality: "low" | "high" | "unable-to-assess"}`.
Like all clinical entities, allergies belong to an open encounter and start
at revision 1.

## Correction, not editing

`POST /records/:id/correct`, `{version, data: {reason, ...}}`. This is the
same correction mechanism used for observations and conditions
(`.claude/rules/clinical-safety.md`): the previous version is retained and
marked `entered-in-error`; the correction is a **new** record. There is no
in-place edit of a finalized allergy.

## The absence rule

An allergy list with zero entries is displayed as **unknown**, never as
confirmed absence of allergies — this is an explicit UX-contract invariant
in [docs/ARCHITECTURE.md](../ARCHITECTURE.md) ("UX Contract") and applies to
every chart renderer. This matters directly for medication reconciliation:
see [medications.md](medications.md) — a reconciliation snapshot includes
allergy entity versions specifically so a reviewer can see whether the
allergy list has changed since the last confirmed review.

## FHIR

Exported as `AllergyIntolerance` — text substance/reaction, status, and
criticality (see [../interoperability/fhir/resources.md](../interoperability/fhir/resources.md)).

## Not implemented

There is no substring-based or coded allergy-interaction decision engine
(explicitly ruled out as a backlog anti-goal in docs/PLAN.md — "no
substring-based allergy decision engine"). Allergy/medication interaction
checking would require a clinically validated drug-knowledge source, not an
engineering default.
