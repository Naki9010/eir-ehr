# Changes Documentation

- [CHANGELOG.md](CHANGELOG.md) — the master, chronological change log
- [architecture-changes.md](architecture-changes.md)
- [database-changes.md](database-changes.md)
- [api-changes.md](api-changes.md)
- [security-changes.md](security-changes.md)
- [clinical-changes.md](clinical-changes.md)
- [interoperability-changes.md](interoperability-changes.md)
- [ux-changes.md](ux-changes.md)

## Source of truth

Entries are derived from actual commit history (`git log`) cross-referenced
against the flat `docs/*.md` files, which document each milestone's
evidence in detail. Git history alone is not documentation (CLAUDE.md
§17/§111) — each CHANGELOG entry adds the why, the affected areas, and the
verification, which commit messages alone do not carry. Going forward,
every meaningful change must add an entry here **and** update the specific
area file(s) below it affects, per the root [CLAUDE.md](../../CLAUDE.md)
"Documentation Is Part Of The Code" rule.
