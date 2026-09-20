# Third-Party Data

See [data-flows.md](data-flows.md) for the full inventory; this page focuses
specifically on data crossing to systems outside Eir's own deployment.

## What can leave the deployment boundary today

- **AI**: only to a locally configured Ollama endpoint (loopback by default;
  the adapter rejects non-loopback endpoints and redirects) or the built-in
  extractive (no-network) provider. No external model API is called by
  default, and none is a required/paid dependency
  (README.md "no proprietary application core or mandatory paid model API").
- **Lab transport / notification gateway / external risk model**: only to
  operator-configured HTTPS endpoints, each with its own scoped credential —
  see [../interoperability/integrations/authentication.md](../interoperability/integrations/authentication.md).
  Default demo/staging profiles configure **no** external destinations.
- **Hosting infrastructure**: the public demo's dynamic component runs on
  Google Cloud Run in `europe-north1` (Finland); Firebase Hosting's CDN is
  global. This is explicitly documented as *not* a claim of exclusive EU
  processing or a healthcare data-residency solution
  (docs/HOSTING.md).

## What never leaves

Backup archives are encrypted client-side before being written
(AES-256-GCM) — there is no plaintext transmission step. The audit chain,
session store, and workforce/assignment data are not exported anywhere.

## Processing agreements

No deployment-specific data-processing agreement, sub-processor list, or
international-transfer (GDPR Chapter V) assessment has been completed for
any of the above — this is explicit open work for the deploying
organization, not something this documentation can complete on its behalf
(docs/REGULATORY-ALIGNMENT.md).
