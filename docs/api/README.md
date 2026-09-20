# API Documentation

Primary source: [docs/API.md](../API.md) (the authoritative, detailed
contract — this folder summarizes and cross-references it rather than
duplicating the full endpoint table). Also authoritative at runtime: the
profile-aware `GET /api/openapi.json`, generated from the same validators
that enforce requests.

## Contents

- [endpoints.md](endpoints.md) — grouped summary of routes (full table: [docs/API.md](../API.md))
- [authentication.md](authentication.md) — how a request is authenticated
- [authorization.md](authorization.md) — how a request is authorized
- [errors.md](errors.md) — error shape and status codes
- [versioning.md](versioning.md) — API versioning approach (there isn't a URL version scheme; see why)

## Base facts

Base: `http://127.0.0.1:4180/api` (default local profile). JSON requests and
responses; FHIR export uses `application/fhir+json`. Every `/api` endpoint
requires `Authorization: Bearer <session>` (or, on the OIDC clinic profile,
an HttpOnly session cookie plus exact-Origin check for writes). Request
bodies are capped at 128 KiB. No SMART/OAuth discovery is advertised by the
local identity profile.
