---
name: pr-checklist
description: Prepare an Eir EHR change for pull request exactly the way CONTRIBUTING.md requires — commands to run, DCO sign-off, and what the PR description must explain. Use before opening or finalizing a PR.
---

# PR Checklist

Straight from [CONTRIBUTING.md](../../../CONTRIBUTING.md) — don't improvise a different bar.

## Before opening the PR

1. Read `docs/PLAN.md` and existing issues; for a substantial change, there should already be an issue with workflow, scope and acceptance criteria.
2. Work on a focused feature branch, not a grab-bag of unrelated changes.
3. Implementation includes tests. Module boundaries and existing clinical data semantics are preserved — check with `architect` and `clinical-safety` agents if unsure.
4. Migrations and API changes are documented (`docs/*.md` updated in the same change — see the `docs` agent).

## Required commands (must all be run, not assumed)

```sh
npm ci
npm run terminology:import
npm run check
npx playwright install chromium
npm run test:e2e
npm run format:check
```

Run all six — CONTRIBUTING.md's "Review Standards" section lists them together; don't stop at `npm run check` alone.

## Commit requirements

- Apache-2.0-compatible contribution only.
- Sign off every commit with the Developer Certificate of Origin: `git commit -s`.

## What the PR description must explain

- What user-facing behavior changed.
- Any contract change (API shape, plugin `provides`/`requires`, storage schema).
- Migration impact, if any.
- What was actually tested, and what remains uncertain.
- Any clinical or security risk introduced, however small — don't omit a risk because it seems minor.
- Screenshots for any UI change.

## Data discipline

Only synthetic fixtures. Never commit a database file, a credential, a real record's patient identifier, identifiable clinical text, or a downloaded terminology dataset (`.terminology/`, `.data/`, `*.sqlite*`, `.env*` are already gitignored — don't work around that).

## Review bar to expect

Security, record-lifecycle, migration, and clinical-semantics changes require maintainer review; PLAN.md's Open Governance section calls for two-person review on authorization/record-lifecycle/migration changes specifically — don't be surprised if one reviewer isn't enough for those.

## New plugins/connectors specifically

Don't label a connector "operational" until it has passed its service-specific checks against an authorized test environment — a configured endpoint is not a working connection (see the `integration` and `plugin-author` agents).
