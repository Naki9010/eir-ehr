# Care-Team Scheduling

Primary source: [docs/CARE-TEAM.md](../CARE-TEAM.md).

## Purpose

Daily booking/check-in, a shared assigned inbox with explicit handover, and
encounter-linked appointment completion, with server-autosaved drafts.

## User Workflow

Book an appointment (Arbetslista) → arrive/start reuses or creates the
patient's open encounter atomically → sign notes and close the encounter →
the linked appointment completes in the same transaction. Tasks (Inkorg)
are filtered by owner/status; only the current owner starts/completes/
cancels; a colleague first records explicit reassignment.

## Architecture

`eir.care-team`/`CareTeam` (no SQL — uses the shared transactional entity/
version/audit path), `apps/web/care-team.js`, `apps/web/draft-editor.js`.
See [../architecture/backend.md](../architecture/backend.md).

## Data Model

`appointment` and `task` entity kinds; roster (`members`) is tenant-scoped,
operator-controlled configuration, not a separate credentialed identity.

## API

`GET /care-team?day=...`, `POST /patients/:id/appointments`,
`POST /appointments/:id/:action`, task creation/transition routes. See
[docs/API.md](../API.md) "Care-team additions".

## Authorization

`schedule.write` for appointments, `task.write` for tasks. A roster entry
never itself grants chart access — every query calls audited `Access.check`.

## Security

Scheduling uses the Temporal polyfill in the configured IANA clinic
timezone, never server/browser local zone; nonexistent/ambiguous DST times
are rejected, not guessed.

## Privacy

Internal tasks and appointments are excluded from patient/proxy chart,
history, and changes.

## Clinical Safety

A second appointment cannot share an in-progress encounter; only the
booked clinician may start it; no-show rejects future/arrived bookings.

## Audit

Draft autosave, reassignment, and appointment actions are all versioned and
audited like any other clinical mutation.

## Interoperability

Appointments are **not yet** mapped into the FHIR export.

## Error Handling

Draft-save conflicts preserve unsaved local text and require explicit
confirmation before replacing it with the server version — never a silent
overwrite.

## Concurrency

Overlap checks cover the patient and clinician across all active tenant
bookings; expected-version checks on every task/appointment transition.

## Testing

`tests/care-team.test.ts`, `tests/care-team.e2e.ts`.

## Known Limitations

Single-process SQLite is still the reference deployment for this feature's
tests; no availability calendars, rooms/equipment, recurrence, or waiting
lists.

## Future Improvements

PostgreSQL-backed production deployment, stronger workforce identity,
protected-identity policy, independent clinical validation — see
[docs/CARE-TEAM.md](../CARE-TEAM.md) "Verification And Limits".
