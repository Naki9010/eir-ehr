# API Authentication

See [../security/authentication.md](../security/authentication.md) for the
full treatment. Summary for this folder's context:

- Every `/api` route requires `Authorization: Bearer <session>` (local
  profiles) or an HttpOnly session cookie (OIDC clinic profile, with exact
  `Origin` match required for writes).
- Browser login for the OIDC profile uses `/auth/login` and `/auth/callback`
  — outside `/api`, since they're not themselves authenticated clinical
  operations.
- Integration result intake (`/integrations/:connectorId/results`) uses a
  **separate** machine bearer credential, scoped to that connector only —
  see [../interoperability/integrations/authentication.md](../interoperability/integrations/authentication.md).
  It cannot authenticate at `/api/session`.
- Client-supplied actor/role/tenant headers are always ignored; the actor is
  resolved entirely server-side from the token/cookie.
