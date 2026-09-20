# Patient Context

Primary source: [docs/ARCHITECTURE.md](../ARCHITECTURE.md) ("UX Contract"),
CLAUDE.md §9–§10.

## The invariant

Patient identity remains visible through every chart view — this is a
stated UX-contract requirement, not a per-screen styling choice, and it
applies uniformly across both bundled chart renderers (timeline and table;
see [tables.md](tables.md)).

## How wrong-patient risk is structurally reduced

- The frontend never assumes "still the same patient as last request" —
  every API call names the patient ID explicitly, and the backend
  independently re-authorizes it every time (see
  [../clinical/patient-safety.md](../clinical/patient-safety.md) "Wrong-
  patient safety").
- No bearer token or record data is cached in `localStorage`/
  `sessionStorage`/IndexedDB, which limits how stale cross-tab state can
  leak between patients on a shared browser profile.
- A version conflict (`409`) always surfaces as a reload-required error —
  the UI never silently merges or overwrites, which also prevents a stale
  background tab from quietly re-asserting an old patient's data over a
  newer edit.

## What is explicitly tested

Switching patients, opening multiple patients in different tabs, stale/
cached patient pages, browser back/forward, copied URLs, concurrent tabs,
and delayed/stale API responses arriving after a patient switch — see
[../testing/clinical-safety-tests.md](../testing/clinical-safety-tests.md).
