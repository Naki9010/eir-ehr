# API Versioning

## Current state: no URL-path API version scheme

There is no `/api/v1/`, `/api/v2/` style versioning. The API contract is
version `0.1` at the document level (docs/API.md's title, "API Contract
v0.1") but this is a documentation label, not an enforced runtime version
negotiated per-request.

## What *is* versioned

- **Plugin runtime API** (`apiVersion: 2` in every plugin manifest) — a
  breaking contract change at the plugin-composition level, checked at
  startup. See [../architecture/system-overview.md](../architecture/system-overview.md).
- **Plugin semantic versions**, visible per-plugin at `GET /api/plugins`.
- **Entity revisions** (`version` field on every entity) — this is
  optimistic-concurrency versioning, not API versioning; see
  [../database/concurrency.md](../database/concurrency.md).
- **Terminology release version** (ICD-10-SE release date/checksum),
  returned in diagnosis search responses and persisted on every condition
  record.
- **FHIR resource `versionId`/`meta.lastUpdated`** on exported resources.

## Backward compatibility approach

Rather than URL versioning, the project's stated approach (CLAUDE.md §102,
docs/PLUGINS.md) is: prefer backward-compatible evolution of the existing
contract where practical, and treat any breaking change to storage or
clinical contracts as requiring a documented migration and updated tests —
the runtime-API-v2 transition (see [../database/migrations.md](../database/migrations.md))
is the concrete precedent for how a breaking change is actually handled
here: an explicit version bump, startup rejection of the old version, and a
written migration note, not silent dual-support.

## Gap

If Eir ever needs to support multiple API consumer versions concurrently
(e.g. an older frontend build against a newer backend), that would require
a deliberately designed versioning scheme not currently present — this is
not an oversight to route around with an undocumented convention.
