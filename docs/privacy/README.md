# Privacy Documentation

Primary source: [docs/OPERATIONS.md](../OPERATIONS.md),
[docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md) ("Privacy and
trust boundaries"), [.claude/rules/privacy.md](../../.claude/rules/privacy.md).

## Contents

- [data-classification.md](data-classification.md) — what counts as PHI/sensitive here
- [data-flows.md](data-flows.md) — system-by-system inventory of where patient data goes
- [phi-handling.md](phi-handling.md) — minimization principles applied in practice
- [logging.md](logging.md) — what the server logs and deliberately excludes
- [telemetry.md](telemetry.md) — analytics/telemetry (none exists)
- [retention.md](retention.md) — retention policy status (not yet defined)
- [third-party-data.md](third-party-data.md) — what leaves the deployment boundary

## Governing principle

Eir does not assume a data flow is acceptable simply because it works
technically (CLAUDE.md §70). This documentation set states what is
implemented, what is minimized by design, and — just as importantly — what
retention/deletion/DPIA decisions remain genuinely unresolved and must not
be treated as settled. See
[docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md) for the full GDPR/
Patientdatalagen applicability discussion; no deployment-specific DPIA,
DPA, or transfer assessment has been approved for this project.
