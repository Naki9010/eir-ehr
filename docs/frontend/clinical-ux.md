# Clinical UX

Primary source: [docs/ARCHITECTURE.md](../ARCHITECTURE.md) ("UX Contract"),
CLAUDE.md §35.

## Invariants (not preferences — these are load-bearing for patient safety)

- The workflow is register → open encounter → capture facts → draft →
  review → sign → follow up → close (see
  [../clinical/clinical-workflows.md](../clinical/clinical-workflows.md)).
- An allergy absence displays as **unknown**, never as confirmed absence.
- Notes always display draft/signed state and attribution.
- A version conflict is a reload-required error — never a silent overwrite.
- The interface never implies an action succeeded when the backend
  rejected it (CLAUDE.md §34) — every mutating action's UI state follows
  the actual server response, not an optimistic assumption.

## Design priorities (CLAUDE.md §35)

Clarity, speed, scanability, low cognitive load, predictable navigation,
obvious patient context, strong hierarchy, keyboard efficiency, clear
status, minimal unnecessary clicks — and explicitly avoiding excessive
animation, confusing navigation, excessive modals, ambiguous actions, tiny
text, color-only warnings, hidden patient context, and clutter.

## Before a clinical pilot

[docs/ARCHITECTURE.md](../ARCHITECTURE.md) sets an explicit validation
requirement: test real workflows with GPs, nurses, reception staff, and
patients, including protected identities, duplicate identities, uncertain
identifiers, late results, interrupted sessions, and staff handoffs — and
measure task completion/omission rates, not aesthetic preference alone.
This has not yet happened.
