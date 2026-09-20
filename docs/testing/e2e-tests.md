# E2E Tests

Primary source: `README.md` ("Develop"), `tests/*.e2e.ts`.

## Runner

`npx playwright install chromium` then `npm run test:e2e`
(`tsx --test tests/*.e2e.ts`). Tests use an isolated in-memory database and
exercise clinician workflows at desktop and mobile viewport sizes,
screenshotting to `test-results/`.

## Files

`workflow.e2e.ts` (core clinical loop), `care-team.e2e.ts`,
`coordination.e2e.ts`, `deterioration.e2e.ts`, `follow-up.e2e.ts`,
`integrations.e2e.ts`, `medication-results.e2e.ts`, `oidc.e2e.ts`,
`postgres-workflow.e2e.ts`, `public.e2e.ts`, `social.e2e.ts`,
`workforce.e2e.ts`.

## Public-deployment smoke path

`npm run smoke:public -- <url>` plus
`EIR_DEMO_TEST_URL=<url> npm run test:e2e` runs the same browser suite
against a **deployed** instance with synthetic data, logging out afterward
— used as the pre-release check for the public demo (docs/HOSTING.md
"Before Real Clinical Use"). The normal local suite still uses its isolated
fixture database; this is a distinct, additional check, not a replacement.

## Social-preview crawler test

`social.e2e.ts` checks Open Graph/X-card metadata without JavaScript under
Facebook/LinkedIn/X crawler user agents — a narrow but real test of a
narrow but real feature (docs/SHARING.md).
