# Data Flows

System-by-system inventory of where patient-related data goes, compiled from
the flat docs and source. See [../security/threat-model.md](../security/threat-model.md)
for the corresponding trust-boundary view.

| Flow | What is sent | Where | Minimization |
| --- | --- | --- | --- |
| Browser ↔ API | Authenticated session token, clinical form payloads | Same-origin HTTPS (loopback in dev) | No bearer token or record content in `localStorage`/`sessionStorage`/IndexedDB |
| API → AI provider | Bounded, authorized encounter-context evidence snapshot | Local extractive logic, or local Ollama at a loopback endpoint | Provider receives only the bounded evidence, never the full chart; non-loopback Ollama endpoints/redirects are rejected by the adapter |
| API → FHIR export | Full authorized-visibility Bundle | Returned to the authenticated caller only, on request | Visibility filtering via `packages/visibility.ts`; export itself audited |
| Lab order dispatch | Minimal patient identification snapshot + test/specimen/requester | Configured lab connector endpoint (HTTPS) | Full chart and AI context are never included (docs/INTEGRATIONS.md) |
| Lab result intake | Analyte results, source-supplied flags | Received from configured connector via machine credential | Scoped to one connector/tenant/unit; quarantined if unmatched |
| Follow-up/coordination notifications | Generic fixed prompt text + workspace URL only | Configured notification gateway (HTTPS) | Explicitly **no** patient name, ID, task ID, result, or clinical text (docs/FOLLOW-UP.md) |
| Deterioration external model | Age, encounter vitals/labs (source-labelled), no direct identifiers | Configured HTTPS risk-model endpoint | Not anonymized — still sensitive; requires its own data-processing arrangement (docs/DETERIORATION.md) |
| Backup | Full encrypted logical database dump | Operator-controlled storage, AES-256-GCM encrypted in transit to disk | No plaintext dump ever created; operator manages key custody (docs/RECOVERY.md) |
| Public demo hosting | Synthetic-only data, in-memory per visitor | Cloud Run (`europe-north1`), Firebase Hosting (global CDN) | No real patient data permitted; national identifiers rejected by the public registration endpoint (docs/HOSTING.md) |

## Data that never leaves the deployment boundary

Coordination (`sam*`) private records, follow-up/coordination coverage and
worker-operational records, and the audit chain itself are excluded from
chart, history, change-feed, and FHIR export paths — see
[../clinical/clinical-workflows.md](../clinical/clinical-workflows.md) and
[../interoperability/fhir/resources.md](../interoperability/fhir/resources.md).

## What is not yet mapped formally

There is no standalone, approved data-flow diagram/register maintained
outside this documentation and the flat `docs/*.md` sources — a deployment-
specific DPIA would need to formalize this table with the actual provider's
processors and legal bases (docs/REGULATORY-ALIGNMENT.md).
