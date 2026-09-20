# Docker

Primary source: `Dockerfile`, [docs/HOSTING.md](../HOSTING.md).

## The image is the public demo, specifically

```dockerfile
FROM node:22-bookworm-slim
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --omit=dev --ignore-scripts && npm cache clean --force
COPY apps ./apps
COPY packages ./packages
COPY scripts/import-icd.ts ./scripts/import-icd.ts
RUN node --import tsx scripts/import-icd.ts
COPY plugins ./plugins
COPY eir.demo.config.json ./
ENV NODE_ENV=production HOST=0.0.0.0 PORT=8080
USER node
EXPOSE 8080
CMD ["node", "--import", "tsx", "apps/public-server.ts"]
```

Notes:

- Terminology import (ICD-10-SE) runs **at build time**, not at container
  start — the image is self-contained with no runtime network dependency
  for terminology.
- `npm ci --omit=dev --ignore-scripts`: no dev dependencies, no install
  scripts executed (supply-chain hardening — see
  [../security/dependency-security.md](../security/dependency-security.md)).
  runs as the non-root `node` user.
- The image hardcodes `eir.demo.config.json` — this container is
  purpose-built for the disposable public demo, not a general-purpose Eir
  container. Running it as anything else (e.g. mounting a real database) is
  not a supported configuration.

## No compose file for the application itself

`compose.staging.yml` provisions **only** the PostgreSQL 18 service for
staging (see [docs/PERSISTENCE.md](../PERSISTENCE.md)); the application
process itself runs via `npm run staging`, not inside that compose file.
There is no Dockerfile variant for a clinic deployment.
