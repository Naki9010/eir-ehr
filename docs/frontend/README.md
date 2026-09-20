# Frontend Documentation

Primary source: [docs/PLUGINS.md](../PLUGINS.md) ("Replace The Chart"),
[docs/ARCHITECTURE.md](../ARCHITECTURE.md) ("UX Contract"),
[docs/CARE-TEAM.md](../CARE-TEAM.md). See also
[../architecture/frontend.md](../architecture/frontend.md) for the
architectural contract this folder assumes.

## Contents

- [navigation.md](navigation.md) — workspace navigation structure
- [patient-context.md](patient-context.md) — how the active patient is kept unambiguous
- [components.md](components.md) — the actual `apps/web/*.js` modules
- [forms.md](forms.md) — form/draft behavior (autosave, conflict handling)
- [tables.md](tables.md) — the table chart renderer
- [accessibility.md](accessibility.md) — what's implemented, what's untested
- [clinical-ux.md](clinical-ux.md) — UX invariants specific to clinical safety

## Stack

Hand-written ES modules, no frontend framework, no bundler step in
development (`apps/web/*.js` served statically). `style.css` for styling.
`lucide` supplies icons. This is a deliberate architectural choice, not an
unfinished migration — see [../architecture/frontend.md](../architecture/frontend.md).
