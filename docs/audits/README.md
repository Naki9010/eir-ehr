# Audits

## Current state: no formal audit reports exist yet

No independent security audit, privacy audit, clinical-safety audit,
interoperability audit, or release audit has been performed and recorded
for this repository as of this writing. The subfolders below are
scaffolded, empty, and ready to receive real reports — they are not
backfilled with invented findings.

- [security/](security/README.md)
- [privacy/](privacy/README.md)
- [clinical-safety/](clinical-safety/README.md)
- [interoperability/](interoperability/README.md)
- [release/](release/README.md)

## What exists instead, today

- **Automated engineering test evidence** — see [../testing/README.md](../testing/README.md).
  This is real, current, and runs in CI, but it is developer-controlled
  synthetic-data testing, not an independent audit (docs/REGULATORY-
  ALIGNMENT.md "Evidence index").
- **A self-assessed regulatory evidence map** —
  [docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md), explicitly
  labelled "draft for clinical, privacy, security and regulatory review,"
  not itself an audit.
- **A named list of unresolved release gates** —
  [docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md) "Clinical
  release gates" and [docs/PLAN.md](../PLAN.md) — these describe what an
  audit would need to check, not the result of having checked it.

## Report format (for when a real audit happens)

```
## Scope
## Date
## Reviewer/Agent
## Findings
## Severity
## Affected Areas
## Remediation
## Verification
## Remaining Risks
```

Each category subfolder's README repeats this template with a pointer back
here, ready for its first real entry.
