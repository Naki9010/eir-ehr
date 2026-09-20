# API Authorization

See [../security/authorization.md](../security/authorization.md) and
[../architecture/authorization.md](../architecture/authorization.md) for the
full permission model. Summary for this folder's context:

- Authorization is checked inside each domain service against the actor
  resolved from authentication — never inferred from a URL parameter alone.
  A patient ID in the URL path is *what's being requested*, not proof of
  authorization to see it.
- `GET /patients` returns only patients accessible to the calling
  principal — there is no separate "list all patients" admin bypass.
- `GET /patients/:id/permissions` returns the caller's own effective
  permissions for that patient, computed the same way the enforcement path
  computes them — useful for the frontend to conditionally render actions,
  but never itself a security boundary (the backend re-checks on every
  actual mutating call, per CLAUDE.md §15/§34).
- Sensitive operations default to deny: an unrecognized or under-permissioned
  actor gets `403`, never a partial/degraded success.
