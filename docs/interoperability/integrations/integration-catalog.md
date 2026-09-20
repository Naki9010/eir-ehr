# Integration Catalog

Primary source: [docs/INTEGRATIONS.md](../../INTEGRATIONS.md),
[docs/FOLLOW-UP.md](../../FOLLOW-UP.md), [docs/SAMVERKAN.md](../../SAMVERKAN.md),
[docs/DETERIORATION.md](../../DETERIORATION.md) ("External model API").

Status terms used below follow CLAUDE.md's required precision: **Designed**
(protocol/schema exists), **Implemented** (working code + local tests),
**Configured** (a profile has connector entries), **Connected** (a real
external endpoint is live), **Tested** (protocol-conformance tests pass
against a real or sandbox HTTP endpoint), **Production** (in clinical use).
No integration below has reached Connected or Production against a real
external partner.

| Integration | Purpose | Direction | Protocol | Status | Auth |
| --- | --- | --- | --- | --- | --- |
| Laboratory order/result (`eir.lab.v1`) | Order dispatch, result intake, corrections | Bidirectional | JSON over HTTPS, `Idempotency-Key` + SHA-256 payload hash | Implemented, Tested (`npm run demo:integrations` local sandbox; `tests/integrations.test.ts`, `tests/integrations.e2e.ts`) | Bearer, separate outbound/inbound tokens per connector |
| Follow-up notification (`eir.notification.v1`) | Generic "open Eir" prompt to staff | Outbound only | JSON over HTTPS, `Idempotency-Key` + payload hash | Implemented, Tested (`tests/follow-up.test.ts`) | Bearer |
| Coordination notification (shared `eir.notification.v1` transport) | Same generic prompt, for Samverkan case participants | Outbound only | Same as above | Implemented, Tested (`tests/coordination.test.ts`) | Bearer |
| External deterioration risk model (`eir.risk.v1`) | Authenticated HTTP inference request/response | Outbound (request/response) | JSON over HTTPS | Implemented as an adapter contract (`plugins/risk-http.ts`, `tests/risk-http.test.ts`); no default external endpoint configured | Bearer |
| FHIR R4 export | Bundle projection for an external system to pull | Outbound (pull, authenticated GET) | `application/fhir+json` | Implemented; not connected to any receiving system | Session bearer (same as clinical API) |
| SITHS / HSA / NPÖ / NLL / Webcert / regional lab / referral systems | Various | — | — | **Not started** — see [docs/SWEDISH-INTEGRATIONS.md](../../SWEDISH-INTEGRATIONS.md) for ownership and onboarding contacts | — |

## Per-integration detail

- **Laboratory**: patient identifiers are matched exactly (no fuzzy name
  matching); corrections require a `supersedesMessageId`; unsafe/unmatched
  messages are durably quarantined, never guessed into a chart. See
  [../../clinical/laboratory-results.md](../../clinical/laboratory-results.md)
  and [retry-and-idempotency.md](retry-and-idempotency.md).
- **Follow-up/coordination notifications**: payload contains **no** patient
  name, ID, result, or clinical text — only a fixed generic prompt and
  workspace URL. Delivery acceptance by the gateway is explicitly not
  clinical acknowledgement (docs/FOLLOW-UP.md, docs/REGULATORY-ALIGNMENT.md).
- **Deterioration external model**: request/response schema is fixed
  (`modelId`, `modelVersion`, `inputHash`, bounded `input`/`output`); the
  provider is responsible for calibration and validated population — Eir
  does not train or certify a model through this interface.
