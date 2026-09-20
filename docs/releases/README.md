# Releases

## Current state: no formal tagged release exists

`package.json` reports version `0.2.0`, but there is no corresponding Git
tag, GitHub Release, or per-version release note in this repository as of
this writing — development proceeds on `main` (see git status: current
branch `main`, no release branches). This folder is scaffolded per the
project's documentation policy, ready for the first real release, rather
than backfilled with an invented release history.

## What to do at the first real release

Create a file here named for the release (e.g. `2026-XX-XX-v0.3.0.md`)
following this template, and link it from this README:

```
## Release
Version/date.
## Summary
## Features
## Fixes
## Security
## Privacy
## Clinical
## Interoperability
## Database
## Breaking Changes
## Testing
## Known Issues
## Rollback
```

Run the full pre-release gate first — see
[.claude/commands/release-check.md](../../.claude/commands/release-check.md)
(`/release-check`) — and cite its actual output in the release note's
Testing/Known Issues sections. Do not mark a release "production-ready" or
"clinically validated" without the evidence described in
[docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md) "Clinical
release gates" — see CLAUDE.md §112 for the required precision.

## Until then

Treat [docs/PROJECT-STATUS.md](../PROJECT-STATUS.md) and
[changes/CHANGELOG.md](../changes/CHANGELOG.md) as the current source of
truth for "what works today."
