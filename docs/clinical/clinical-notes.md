# Clinical Notes

Primary source: [.claude/rules/clinical-safety.md](../../.claude/rules/clinical-safety.md)
("Note lifecycle"), [docs/API.md](../API.md).

## Lifecycle

```
Create draft → Save (repeatable, expected-version checked) → Sign (immutable via DB trigger)
                                                                   │
                                                                   ▼
                                                    Amend = new draft, references original + reason
```

- **Draft**: created via `POST /api/patients/:id/records/note`
  (`{encounterId, text}`), optionally with a client-supplied `clientId` UUID
  — retrying the same patient/author/encounter/text draft returns the
  existing record instead of creating a duplicate; mismatched or already-
  signed content returns `409`.
- **Save**: `POST /records/:id/save` with the currently-read `version` and
  new `text`. Autosave in the care-team UI debounces at 900ms and uses the
  same versioned save path.
- **Sign**: `POST /records/:id/sign`, `{}`. Persists actor and time. A
  **database trigger** — not merely application logic — rejects all
  subsequent updates to a signed note. Only the draft's author can sign it.
- **Amend**: `POST /records/:id/amend`, `{text, reason}`. Creates a *new*
  draft record that references the original and carries a reason; the
  original signed note is never mutated.

A finished (closed) encounter rejects new ordinary notes but still allows
amendments.

## What this guarantees

- A signed note is immutable at the database level, not just by API
  convention — even a bug in application code cannot silently alter it.
- Every note's provenance (author, time, encounter, and for amendments, the
  original it amends) is preserved indefinitely.
- Patient/proxy views omit unsigned draft note versions (`packages/visibility.ts`).

## Not implemented

Co-signing, dictation, template plugins, and patient-release policy for
notes are explicit backlog items (docs/PLAN.md, P1 "Complete note
workflow"). Signing records authenticated application attestation — it is
**not** a qualified electronic signature (eIDAS) or equivalent.
