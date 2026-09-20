# Privacy Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md) §18, §29, §45. This file gives the privacy agent the detailed shape.

## Current baseline (treat as the floor, not a target to negotiate down from)

- No request bodies, authorization headers, identifiers, or records in server logs.
- No records or bearer tokens in `localStorage`; local dev tokens live only in browser memory, OIDC uses HttpOnly cookies.
- Public demo mode: one in-memory, disposable database per visitor, expiry enforced server-side on every API access, never touches the persistent database.
- `.gitignore`/`.dockerignore` exclude `.data/`, `.terminology/`, `*.sqlite*`, `.env*` — nothing patient-shaped should ever be committed.

## Data minimization in integration/AI payloads — the existing pattern to match

- `OrderMessage` (lab integration) carries only a minimal patient identification snapshot, the test/specimen/requester — never the full chart or AI context.
- The deterioration risk snapshot omits direct patient names/identifiers but is still explicitly called "sensitive patient data, not anonymization" — source references, times, and lab text remain identifying in aggregate. Treat pseudonymized snapshots with the same care as identified data.
- AI providers receive a bounded, authorized copy of the current encounter's evidence — never a standing chart credential or unrestricted query capability.

Any new integration, notification, or AI payload should be reviewed against this bar: does it carry the minimum needed for the receiving system to do its job, or did it copy the whole record for convenience?

## Exports

`/api/patients/:id/export/fhir`, coordination CSV/PDF export, and audit paging are the only bulk-export paths today. Visibility must follow the authenticated actor's real access — a clinician export may include drafts/internal tasks they're permitted to see; a patient/proxy export follows the stricter path. `sam*` (Eir Samverkan) private records and deterioration monitoring data are deliberately excluded from FHIR export — don't add them without an explicit, documented decision.

## Development and test data

Every fixture, seed script (`apps/seed.ts`, `scripts/*-sandbox.ts`), and test file uses synthetic data. Never copy a real name, personnummer, clinical note, or lab value into a fixture "just for this test" — generate a synthetic equivalent instead. This also applies to documentation examples and screenshots.

## Logging discipline for new code

Prefer: request ID, correlation ID, resource type, operation name, status, timing, error category. Avoid: names, personnummer, clinical note text, lab/medication detail, tokens, credentials — even at debug level, since debug logging has a way of ending up enabled in shared environments.

## Browser storage discipline for new UI

`localStorage`/`sessionStorage`/IndexedDB may hold non-sensitive UI conveniences (e.g. last-selected chart renderer, collapsed-panel state) but never patient data, session identity, or bearer tokens. Prefer the existing in-memory/cookie session model over adding new client-side persistence.
