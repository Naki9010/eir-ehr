# Patient Identity

Primary source: [docs/API.md](../API.md) ("Patient creation"),
[docs/ARCHITECTURE.md](../ARCHITECTURE.md).

## Identifiers

Patient creation accepts a `name`, `birthDate`, and `identifier: {type,
value}`. Supported types include `local` (any institution-scoped value) and
Swedish national identifiers (`personnummer`, `samordningsnummer`),
validated via the `personnummer` npm package (checksum + date validation,
explicit 12-digit format). Birth date is independently recorded — the system
does not infer gender or assume national identity registration from a
checksum. `packages/contracts.ts`'s `Country` interface
(`identifier(input) → {type, value, system}`) centralizes normalization so a
different country pack (`plugins/country-eu-local.ts`) can supply its own
identifier scheme without touching clinical code.

Uniqueness is enforced at the storage layer: SQLite has
`CREATE UNIQUE INDEX identifier_unique ON entities(tenant,
json_extract(data,'$.identifier.system'), json_extract(data,'$.identifier.value'))
WHERE kind='patient'` (`plugins/storage-sqlite.ts`).

## Protected identity

`patient.protected` is a distinct permission from ordinary `chart.read`.
Unauthorized staff receive no such patient through directory, chart,
history, change feed, or export — not a redacted view, but no record at
all. Temporary "emergency access" (`access.emergency`) still requires
`patient.protected` for a protected patient; it is time-boxed (15 minutes),
read-only, unit-scoped, and generates an audit event. See
[docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md).

## What does NOT exist (explicit gap, not an oversight)

- **No patient merge/unmerge.** This is CRITICAL-risk work (CLAUDE.md §60)
  that does not exist in this codebase and must not be improvised as a
  workaround for duplicate records.
- No local reserve-ID lifecycle, corrected-birth-date workflow, or
  deceased/unknown-patient handling beyond what's described above — tracked
  as P1 backlog in [docs/PLAN.md](../PLAN.md).
- No national protected-person search or safe-contact-channel handling.
- No verified citizen/proxy identity onboarding — the clinic identity
  profile does not yet provide this (docs/FHIR.md).

## Restriction (coarse, deliberately)

The current patient "restriction" toggle (`Access.block`) is intentionally
coarse: it blocks assigned clinician/proxy access while preserving
self-access. It is **not** an implementation of Sweden's unit/provider-
specific record blocks, emergency overrides, or age-dependent proxy rights.
