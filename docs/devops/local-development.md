# Local Development

Primary source: `README.md`.

## Prerequisites

Node ≥22.13 (Node's SQLite API — `node:sqlite` — is experimental in Node
22 and is what `plugins/storage-sqlite.ts` uses).

## Quickstart

```sh
npm ci
npm run terminology:import
EIR_DEMO=1 npm start
```

Open `http://127.0.0.1:4180`. A temporary clinician session is printed in
the terminal (no password). Sessions expire after 8 hours; the token is
hashed server-side and kept only in browser memory. Without `EIR_DEMO=1`,
startup does not seed patient records, and data persists in
`.data/ehr.sqlite`. `PORT=4181` selects another port;
`EIR_CONFIG=/absolute/path/to/profile.json` selects a different plugin
composition.

## Full check before proposing a change

```sh
npm run check          # tsc --noEmit && tests
npm run plugins        # print resolved plugin graph, no DB touched
npx playwright install chromium
npm run test:e2e
```

## Sandbox commands for specific modules

```sh
npm run demo:integrations   # lab-integration sandbox with a real local HTTP round trip
npm run demo:follow-up      # follow-up sandbox with the real worker and a local test gateway
npm run smoke:model -- <model>  # real Ollama adapter smoke test
```

See [../devops/configuration.md](configuration.md) for the full list of
`npm run` scripts and what each does.
