# Clinical Workflows

Primary source: [docs/ARCHITECTURE.md](../ARCHITECTURE.md) ("UX Contract"),
[docs/API.md](../API.md), [docs/CARE-TEAM.md](../CARE-TEAM.md).

## The core loop

Register → open encounter → capture facts → draft → review → sign → follow
up → close. This is the UX contract Eir's screens and API are built around
(`docs/ARCHITECTURE.md`).

1. **Register** (`POST /api/patients`) — creates a patient and a 30-day
   local care assignment for the registering clinician.
2. **Open encounter** (`POST /api/patients/:id/records/encounter`,
   `{reason}`) — each patient has at most one open encounter at a time.
3. **Capture facts** — observations (vitals), conditions (diagnoses,
   ICD-10-SE coded), allergies, tasks, all scoped to the open encounter.
4. **Draft / review / sign** — see [clinical-notes.md](clinical-notes.md).
5. **Follow up** — tasks, lab-order review, deterioration-alert response;
   see [laboratory-results.md](laboratory-results.md),
   [../features/follow-up.md](../features/follow-up.md).
6. **Close** (`POST /records/:id/close`) — a finished encounter rejects new
   ordinary notes; amendments remain possible.

## Care-team scheduling layer (docs/CARE-TEAM.md)

Daily booking/check-in ("Arbetslista"), a shared assigned inbox with
explicit handover ("Inkorg"), encounter-linked appointment completion (
closing the linked clinical encounter completes the appointment atomically,
in the same transaction), and server-autosaved note drafts (900ms debounce,
retryable creation, conflict-preserving reload). See
[../features/care-team-scheduling.md](../features/care-team-scheduling.md).

## Multi-unit coordination layer (Eir Samverkan, docs/SAMVERKAN.md)

An optional, per-unit-activated module for shared cases across primary
care/hospital/municipality units within one tenant: consented case sharing,
message-based admission/discharge workflow, structured SIP (samordnad
individuell plan), attachments/PDFs, and payment estimation. See
[../features/samverkan-care-coordination.md](../features/samverkan-care-coordination.md).

## What every workflow shares

- Optimistic concurrency: every mutating action requires the currently-read
  `version`; a stale version is a `409`, never a silent overwrite.
- Reason capture on corrective/administrative actions (amend, correct,
  reassign, cancel, coverage, grant).
- An audit event for every clinically meaningful transition.
