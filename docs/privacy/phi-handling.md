# PHI Handling

Primary source: [docs/OPERATIONS.md](../OPERATIONS.md), CLAUDE.md §18, §108.

## Minimization principles actually applied in code

- **Logs**: server request logging never includes secrets, records, or
  identifiers (docs/API.md, docs/OPERATIONS.md) — see [logging.md](logging.md).
- **Errors**: never expose stack traces, SQL, secrets, or unnecessary PHI
  (see [../api/errors.md](../api/errors.md)).
- **Browser storage**: no records or bearer tokens in `localStorage`,
  `sessionStorage`, or IndexedDB.
- **Third-party payloads**: notification payloads carry no patient data at
  all (see [data-flows.md](data-flows.md)); lab order dispatch carries only
  a minimal identification snapshot, not the full chart.
- **AI evidence**: bounded to the authorized encounter, not the full chart
  or cross-patient history (see [../ai/README.md](../ai/README.md)).
- **Visibility filtering**: centralized in `packages/visibility.ts` rather
  than scattered per-endpoint redaction logic, reducing the chance of an
  inconsistent leak.

## What "minimized" does not mean here

Minimization is an engineering pattern applied consistently in the code
paths above — it is not a substitute for a legally reviewed data-processing
basis, retention schedule, or DPIA. See
[docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md) for what remains
open.
