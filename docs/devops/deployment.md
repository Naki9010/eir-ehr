# Deployment

Primary source: [docs/HOSTING.md](../HOSTING.md) (the authoritative,
detailed procedure).

## Public demo topology

Spaceship DNS (`ehr.eir.space`) → Firebase Hosting (custom domain, managed
HTTPS, static assets) → Cloud Run service `eir-ehr-demo` in
`europe-north1` for `/api/**`, `/demo/**`, `/deployment.json`, `/health`.

Cloud Run: request-based billing, min instances 0, max instances 1, 1 vCPU,
512 MiB, concurrency 40, 60-second timeout, dedicated runtime service
account with **no project IAM roles**. `EIR_PUBLIC_ORIGINS` sets the exact
allowed HTTPS origins (handles the Firebase reverse proxy without trusting
forwarded host headers — not itself authentication).

## Deployment order (must be followed in this sequence)

1. Deploy the backend (Cloud Run revision).
2. Smoke its session/chart/write paths.
3. Deploy static Hosting assets.
4. Repeat the smoke test **through Hosting**.

UI and API are compatible only within the same release — old sessions can
be lost across a deployment. Rollback is: restore the previous Cloud Run
revision **and** the matching Firebase Hosting release together, not
independently.

## Clinic deployment: no scripted pipeline exists

There is no equivalent scripted deployment for
`eir.clinic.config.example.json`. A real deployment requires an operator to
provision infrastructure, TLS, PostgreSQL (see
[docs/PERSISTENCE.md](../PERSISTENCE.md)), and OIDC registration manually,
following [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md) and
[docs/OPERATIONS.md](../OPERATIONS.md) — this is deliberate: CLAUDE.md §80
("Never assume production configuration") applies directly here.

## No guaranteed zero-cost ceiling

Even for the free-tier-eligible public demo, there is no guaranteed
zero-cost ceiling — builds, retained container images, logging, and
network egress can be billed separately (docs/HOSTING.md "Cost Controls").
