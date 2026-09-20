# ADR-0007 — Do Not Claim Plugin Sandboxing; Document The Trust Boundary Instead

## Status
Accepted

## Date
2026-09-18, reaffirmed across every subsequent release (per
[docs/ARCHITECTURE.md](../ARCHITECTURE.md), [docs/PLUGINS.md](../PLUGINS.md),
[docs/OPERATIONS.md](../OPERATIONS.md), [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md))

## Context

The plugin architecture's `requires`/`provides` dependency declarations
could be mistaken for a security boundary — as if a plugin that doesn't
declare `requires: ['store']` therefore cannot access the database. In
reality, since plugins are ordinary in-process JavaScript/TypeScript
modules, any plugin can `import` filesystem or network APIs directly,
regardless of its declared dependencies.

## Decision

Never describe the plugin dependency system as a security sandbox, in code
comments, documentation, or product claims. Explicitly and repeatedly
document the actual trust model: server plugins and UI bundles are
reviewed, operator-installed, privileged in-process code. Untrusted,
multi-vendor extensions require a genuinely separate process/container
with a restricted API principal and controlled egress — which Eir does not
currently provide.

## Alternatives Considered

- Build a real sandbox (e.g. a separate worker process/container per
  untrusted plugin, a restricted API surface, network egress controls) —
  not rejected as a future direction, but explicitly out of scope for the
  current release; doing this properly is substantial, dedicated work, and
  attempting a partial version would risk creating a false sense of
  security worse than clearly documenting the current honest boundary.
- Silently imply stronger isolation than exists (e.g. by naming the
  mechanism "plugin sandbox") — rejected as a direct violation of CLAUDE.md
  §50 ("Never claim... secure... unless there is evidence") and §106 ("No
  Fake Implementation").

## Why

An inaccurate security claim here is worse than an honest gap: a deploying
organization that believed plugins were sandboxed might install a
lower-trust third-party plugin than they otherwise would, with full
application privileges as the actual (undisclosed) consequence.

## Consequences

**Positive**: every security review in this repository starts from an
accurate trust model, reducing the chance of a downstream false sense of
security.

**Negative**: Eir currently has no answer for genuinely untrusted,
multi-vendor plugin marketplaces — this remains an open architectural gap,
named explicitly in [docs/PLAN.md](../PLAN.md) "Open Governance" ("Avoid
auto-installing marketplace code into a clinical runtime").

## Security Impact

This *is* the security decision. See
[../architecture/system-overview.md](../architecture/system-overview.md)
"Trust model" and [../security/README.md](../security/README.md).

## Privacy Impact

Ensures privacy reviews correctly treat every installed plugin as capable
of accessing any data the process can reach, not just its declared
dependencies.

## Clinical Impact

None directly.

## Interoperability Impact

None directly.

## Reliability Impact

None directly.
