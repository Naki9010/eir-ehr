# Forms

Primary source: [docs/CARE-TEAM.md](../CARE-TEAM.md) ("Use").

## Draft autosave (`draft-editor.js`)

Note draft text autosaves after 900ms of inactivity. Saves are serialized
and use expected versions — they never sign the note. Initial creates
include a client-supplied retry identifier (`clientId`) to prevent duplicate
drafts after an uncertain network response. A save conflict (someone else
also revised the draft) preserves the unsaved local editor text and shows
the saved version; replacing local edits with the server version requires
explicit user confirmation, never an automatic overwrite. Closing the editor
or pressing Escape flushes pending edits; a failed save keeps the editor
open rather than silently discarding text. Browser reload while the editor
is dirty warns about unsaved changes.

## What autosave does not claim

This is recovery of *server-confirmed* saves, not offline storage — an
unacknowledged keystroke is not guaranteed to survive a browser crash. No
patient text or bearer token is stored in `localStorage`/`sessionStorage`/
IndexedDB (see [../privacy/phi-handling.md](../privacy/phi-handling.md)).

## Other forms

Registration, encounter/observation/condition/allergy/task creation, and
every domain-specific form (medication, lab order/review, appointment
booking, coordination case/SIP) follow the same pattern: client-side
validation for immediate feedback, but the server-side typed schema is
always the authoritative validator — the frontend never assumes a payload
is valid just because its own form logic accepted it.
