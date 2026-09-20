# Telemetry

## Current state: none

No analytics, telemetry, or usage-tracking integration exists in this
repository. The public demo's social-sharing metadata
([docs/SHARING.md](../SHARING.md)) is static Open Graph/X-card markup for
link previews — it collects nothing about visitors and requires no
JavaScript or account. No third-party analytics script is loaded by
`apps/web`.

If telemetry is ever added, CLAUDE.md §42 and §108 apply: correlation IDs
and operational metrics only, never patient content, and it must not become
a channel for unnecessary PHI exposure. See
[../architecture/observability.md](../architecture/observability.md) for
the current (also empty) state of metrics/tracing more broadly.
