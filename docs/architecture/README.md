# Architecture Documentation

Primary source: [docs/ARCHITECTURE.md](../ARCHITECTURE.md) (decision baseline
2026-09-18) and [docs/PLUGINS.md](../PLUGINS.md) (plugin authoring). This folder
breaks that material into focused topics and adds cross-cutting views the flat
doc doesn't isolate on its own. See also
[.claude/rules/architecture.md](../../.claude/rules/architecture.md), the
architect agent's detailed rule file.

## Contents

- [system-overview.md](system-overview.md) — the plugin runtime, composition roots, contracts, trust model
- [frontend.md](frontend.md) — the browser workspace and chart-renderer contract
- [backend.md](backend.md) — API layering, domain plugins, service boundary
- [database.md](database.md) — storage providers and the record/transaction model (see also [database/](../database/README.md))
- [authentication.md](authentication.md) — identity plugins and session model
- [authorization.md](authorization.md) — the `Access`/`Workforce` policy model
- [audit.md](audit.md) — append-only audit chain and review surface
- [security.md](security.md) — architectural security posture (see also [security/](../security/README.md))
- [privacy.md](privacy.md) — architectural privacy posture (see also [privacy/](../privacy/README.md))
- [reliability.md](reliability.md) — architectural reliability posture (see also [reliability/](../reliability/README.md))
- [observability.md](observability.md) — what is and isn't currently observable
- [decisions/](decisions/README.md) — links to architecture-relevant ADRs

## One-paragraph summary

Eir is a single Node/TypeScript codebase composed at startup from small
plugins that each declare `id`, `version`, `apiVersion`, `provides`, and
`requires`, resolved by a runtime in `packages/runtime.ts` against shared
contracts in `packages/contracts.ts`. A deployment profile (`eir.config.json`,
`eir.demo.config.json`, `eir.staging.config.json`, or a clinic-specific file)
is a JSON list of `{module, config}` entries; swapping a provider (storage,
identity, AI, risk engine, transport) is a profile edit and restart, not a
runtime hot-swap. There is no build step in development — `tsx` runs
TypeScript directly. See [system-overview.md](system-overview.md) for the
full picture and the trust-model caveat that matters for every security
review: plugins are trusted, privileged, in-process code, not a sandbox.
