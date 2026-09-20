# Errors

Primary source: [docs/API.md](../API.md).

## Shape

```json
{ "error": "message", "fields": { "...": "optional per-field detail" } }
```

## Status codes

| Status | Meaning |
| --- | --- |
| `401` | Invalid or expired session |
| `403` | Authorization denied |
| `404` | Missing record |
| `409` | Revision or lifecycle conflict (stale version, already-signed note, duplicate identity with changed content, etc.) |
| `422` | Validation failure (including domain-specific ones like an unsupported diagnosis code or a stale terminology release version) |

## What errors never contain

Stack traces, SQL, secrets, internal infrastructure detail, or unnecessary
patient information (CLAUDE.md §33, §41). Development logs may carry more
technical detail server-side, but the same PHI-minimization rule applies
there too — see [../privacy/logging.md](../privacy/logging.md).

## `409` is a deliberate, frequent, meaningful response

Because so much of Eir's clinical-safety design rests on optimistic
concurrency (see [../database/concurrency.md](../database/concurrency.md)),
`409` is not an edge case to paper over in the frontend — it is the correct
outcome whenever two actors raced, and the UI's job is to surface the newer
state and let the user decide, never to retry-and-overwrite automatically.
