# API Tests

The unit/contract test files in `tests/*.test.ts` (see
[unit-tests.md](unit-tests.md)) exercise the HTTP API contract directly —
there is no separately named "API test" file category in this repository;
API-level assertions (status codes, error shapes, schema validation) are
embedded within each domain's test file (e.g. `security.test.ts` asserts
`401`/`403` behavior across authenticated/unauthenticated/wrong-role/wrong-
tenant/wrong-patient scenarios; `workforce.test.ts` asserts permission
enforcement per endpoint).

`GET /api/openapi.json` is generated from the same request-validation
schemas the tests exercise — there is no separately maintained, potentially
drifting OpenAPI spec file to keep in sync (see
[../api/README.md](../api/README.md)).
