# Plugin Authoring Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md). This file gives the `plugin-author` agent (and the `add-a-plugin` / `provider-replacement-contract` skills) the detailed shape. See also `.claude/rules/architecture.md` for the broader plugin-system design.

## The manifest schema (verified directly against `packages/runtime.ts`)

```ts
const manifest = z.object({
  id: z.string().regex(/^[a-z][a-z0-9.-]+$/),
  version: z.string().regex(/^\d+\.\d+\.\d+$/),
  apiVersion: z.literal(2),
  provides: z.array(serviceName).min(1),
  requires: z.array(serviceName),
});
```

`Runtime.start` parses every plugin against this before wiring anything. Failure modes to know, because they're real thrown errors, not documentation aspiration:

- `Duplicate plugin: <id>` — two plugins with the same `id` in one profile.
- `Duplicate provider: <name>` — two plugins declaring the same `provides` entry.
- `Missing or cyclic plugin dependencies` — the dependency graph can't be resolved (a `requires` that nothing `provides`, or a cycle).
- `Undeclared dependency: <name>` — a plugin's `setup` called `ctx.get(name)` for a service it didn't list in `requires`.
- `Invalid service registration: <name>` — a plugin's `setup` called `ctx.provide(name, ...)` for a service not in its own `provides`, or called it twice for the same name.
- `Incomplete plugin: <id>` — `setup` returned without supplying everything it declared in `provides`.

## Writing `setup(ctx)`

```ts
setup(ctx) {
  const dep = ctx.get('someRequiredService');   // only for names in `requires`
  const conn = /* acquire a resource */;
  ctx.onDispose(async () => { await conn.close(); }); // register cleanup immediately after acquisition
  ctx.provide('myServiceName', { /* implementation */ }); // only for names in `provides`
  // optionally: return a disposer instead of/in addition to onDispose, for simple cases
}
```

Register `onDispose` **as soon as** a resource is acquired — not at the end of `setup` — so a later failure in the same `setup` call still triggers cleanup for what was already acquired (the runtime's `catch` block in `start()` calls `stop()` on any startup failure, running whatever disposers were registered so far).

## Required behavioral contract tests for any new or replacement provider

From docs/PLUGINS.md, verified as the standing bar for any provider swap:

1. Happy-path contract behavior — the interface's documented behavior actually works.
2. Wrong-tenant denial — a request scoped to another tenant is rejected.
3. Missing/expired grants — an unauthorized or expired caller is rejected.
4. Record revisions — optimistic-concurrency/version behavior is correct.
5. Rollback on failure — a failed operation leaves no partial state.
6. Start/stop cleanup — `onDispose` actually releases what was acquired; no leaked connections/timers/listeners after `runtime.stop()`.
7. Missing dependencies — the plugin fails clearly at startup if a `requires` service isn't available, rather than misbehaving at call time.

**Additional for AI/model providers**: timeouts, output-size limits, invalid-source rejection, stale-context handling, prompt-injection resistance, clinician-rated output evaluation (separate from the mechanical tests above).

**Additional for UI/renderer replacements**: keyboard navigation, mobile layout, patient-context visibility, error-state handling.

## Documentation bar (CONTRIBUTING.md: "must document trust, dependencies, cleanup, license and failure modes")

A new `plugins/*.ts` file should make each of these traceable — in a module comment, a linked doc, or both:

- **Trust**: what this plugin can do given its privileges, and what it must never be allowed to do (e.g., no signing/shell/SQL capability handed to a model).
- **Dependencies**: which services it `requires`, and why each is needed.
- **Cleanup**: what `onDispose` releases and what happens if cleanup itself fails.
- **License**: for any wrapped third-party library — see the `dependency-governance` agent.
- **Failure modes**: startup-time vs. call-time failure behavior for each external dependency (network, database, filesystem).

## Registering in a profile

Module paths in `eir.*.config.json` resolve relative to the profile file itself, not the repo root. A new plugin is added to the specific profile(s) that need it — `eir.config.json` (legacy dev), `eir.demo.config.json` (public demo), `eir.staging.config.json` (persistent staging), `eir.clinic.config.example.json` (clinic template) are different deployment contexts with deliberately different plugin sets; don't add a new plugin everywhere by default. Run `npm run plugins` after any change to confirm the graph still resolves.
