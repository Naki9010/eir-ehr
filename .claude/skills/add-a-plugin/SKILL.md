---
name: add-a-plugin
description: Step-by-step for creating a new compliant plugin/provider in Eir EHR, matching the real manifest contract in packages/runtime.ts. Use when about to write a new plugins/*.ts file.
---

# Add A Plugin

See `.claude/rules/plugin-authoring.md` for full detail; this is the quick walkthrough.

## Steps

1. **Pick the service key(s)** this plugin will provide (e.g. `myFeature`) and confirm no other plugin already provides it in the profile you're targeting (`npm run plugins` prints the resolved graph).
2. **Write the manifest** with a valid `id` (`/^[a-z][a-z0-9.-]+$/`, e.g. `eir.myfeature.local`), a semantic `version` (`x.y.z`), `apiVersion: 2`, and explicit `provides`/`requires` arrays — list every service you'll call `ctx.get` on.
3. **Implement `setup(ctx)`**: acquire resources, call `ctx.onDispose(...)` immediately after each acquisition (not at the end), then `ctx.provide(name, impl)` for every name in `provides` before returning.
4. **Fail loudly at startup**, not silently at call time, if a required environment variable or external dependency is missing.
5. **Register it** in the specific `eir.*.config.json` profile(s) that need it — module paths resolve relative to the profile file. Don't add it to every profile by default.
6. **Document it**: trust boundary, dependencies, cleanup behavior, license (if wrapping a third-party package), and failure modes — CONTRIBUTING.md requires this for every new plugin.
7. **Write the contract tests** from `.claude/skills/provider-replacement-contract/SKILL.md` even for a brand-new (not just replacement) provider — the same bar applies.
8. **Verify**: `npm run plugins` (clean graph), `npm run typecheck`, and a test exercising both the happy path and at least one failure/cleanup path.

## Common mistakes this catches

- Forgetting to register `onDispose` before a later acquisition in the same `setup` can fail (leaks the first resource on startup failure).
- Calling `ctx.get` for a service not listed in `requires` (throws `Undeclared dependency`).
- Returning from `setup` without calling `ctx.provide` for every declared `provides` entry (throws `Incomplete plugin`).
- Adding the plugin to every config profile instead of just the ones that need it.
