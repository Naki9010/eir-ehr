# Architecture Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md). This file gives the architect agent (and anyone else) the detailed shape; it does not restate CLAUDE.md's priorities.

## The plugin contract (runtime API v2)

A plugin module exports a default object:

```ts
{
  id: string,            // e.g. "eir.storage.postgres"
  version: string,       // semantic version, visible at /api/plugins
  apiVersion: 2,
  provides: string[],    // service keys this plugin implements
  requires: string[],    // service keys this plugin depends on
  setup(ctx): void | (() => void | Promise<void>)
}
```

`ctx.get(key)` resolves a required dependency (already resolved, in dependency order). `ctx.provide(key, impl)` registers this plugin's implementation. `ctx.onDispose(fn)` registers cleanup that runs in reverse order on shutdown or setup failure. Returning a disposer from `setup` is equivalent to one `onDispose` call for the simple case where initialization can't fail after acquisition.

The runtime resolves the dependency graph, rejects cycles and duplicate providers for the same service key, and only exposes declared dependencies to each plugin — a plugin cannot reach a service it didn't `require`. `npm run plugins` prints the resolved graph without touching the database; run it whenever you add or reorder plugins in a profile.

## Composition roots

Each `apps/*.ts` entry point loads exactly one `eir.*.config.json` profile via `packages/runtime.ts`'s `fromConfig`, and wires up a server or worker around it:

| File | Profile | Purpose |
|---|---|---|
| `apps/server.ts` | `eir.config.json` (default) | Legacy local dev server, SQLite, loopback |
| `apps/staging-server.ts` | `eir.staging.config.json` | Persistent synthetic staging, PostgreSQL, loopback |
| `apps/public-server.ts` | `eir.demo.config.json` | Disposable public demo, one in-memory workspace per visitor |
| `apps/*-worker.ts` | caller-supplied via `EIR_CONFIG` | Background processing (integrations, follow-up, deterioration, coordination) |

A profile is a JSON array of `{module, config}` entries; module paths resolve relative to the profile file. There is no browser endpoint that installs code or edits a profile — changing providers is an operator action plus a restart.

## Contracts live in one place

`packages/contracts.ts` defines every cross-plugin interface (`Store`, `Access`, `Clinical`, `Identity`, `Workforce`, `CareTeam`, `Medications`, `Laboratories`, `Fhir`, `AIReview`, and the chart renderer's `render(target, records)` signature). Domain plugins depend on these interfaces, never on another plugin's concrete class, and never issue SQL directly — only `plugins/storage-*.ts` touches the database.

The `Services` interface can be extended via TypeScript declaration merging for out-of-tree plugins; service keys are validated names, not a fixed allowlist, but every new consumer must still declare what it requires.

## Trust model (do not design around a false assumption)

Server plugins and UI bundles are reviewed, operator-installed code running with full application privileges. Dependency declaration (`requires`/`provides`) is a *programming* contract, not a security sandbox — an in-process plugin can still import filesystem or network APIs directly. A malicious or buggy storage/policy plugin can violate every guarantee this document describes. Multi-vendor untrusted extensions need a separate process/container with a restricted API principal, explicit patient scope, and controlled egress — Eir does not provide that isolation today, and no design should assume it does.

## When to add a new plugin vs. extend an existing one

Add a new plugin/service key when the capability is genuinely independently replaceable (a new storage backend, a new AI provider, a new lab transport). Extend an existing plugin when the capability is a variant of something that already exists (a new resource type in the FHIR export, a new permission in workforce). When in doubt, look at how the existing near-neighbor was built (e.g. `plugins/risk-http.ts` next to `plugins/risk-vitals.ts` for "same contract, alternate implementation") and match that shape.
