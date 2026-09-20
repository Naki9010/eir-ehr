# Navigation

Primary source: [docs/CARE-TEAM.md](../CARE-TEAM.md), [docs/SAMVERKAN.md](../SAMVERKAN.md).

## Workspace sections (Swedish-labelled, as shipped)

- **Patientjournal** — the patient chart (timeline or table renderer).
- **Arbetslista** — daily scheduling/check-in worklist (`care-team.js`).
- **Inkorg** — the assigned follow-up-task inbox.
- **Bevakning** — clinical follow-up oversight workspace
  (`follow-up-workspace.js`; see [docs/FOLLOW-UP.md](../FOLLOW-UP.md)).
- **Samverkan** — multi-unit coordination workspace
  (`coordination-workspace.js`; only visible when the module is enabled for
  the active unit).
- **Moduler** — per-unit optional-module activation
  (`module-workspace.js`; see [docs/DETERIORATION.md](../DETERIORATION.md)
  "Composition").
- **Integrationer** — lab-integration operations view, unit-scoped, no raw
  payloads/credentials shown (`integration-workspace.js`).
- Access/administration views (`access-workspace.js`) for workforce and
  audit review.

Navigation visibility follows what the active plugin composition and the
caller's permissions actually allow — a route or nav item for an
uninstalled module (e.g. deterioration, coordination) simply does not
appear, rather than appearing disabled. This mirrors the API discovery
behavior in [../api/endpoints.md](../api/endpoints.md).
