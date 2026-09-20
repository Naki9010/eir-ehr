---
name: privacy-review
description: Run a focused PHI-exposure pass on an Eir EHR change — logs, telemetry, browser storage, exports, integration/AI payloads. Use before merging anything that adds logging, an export path, or a new integration/AI payload.
---

# Privacy Review

See `.claude/rules/privacy.md` for the full detail this skill draws from.

## Fast checklist

1. Does any new log line, error message, or telemetry event include a name, personnummer, clinical note text, lab/medication detail, token, or credential? (The current baseline logs none of this.)
2. Does any new browser feature write patient data or a session/bearer token to `localStorage`/`sessionStorage`/IndexedDB?
3. Does a new integration or AI payload minimize patient data the way the existing `OrderMessage` and deterioration risk-snapshot do (minimal identification snapshot, not the full chart)?
4. Does a new export path (FHIR, CSV/PDF, audit paging) respect the actor's real visibility (draft/proposal exclusion for patient/proxy views, `sam*` exclusion from chart/FHIR)?
5. Is any identifier or token appearing in a URL query string or path where it shouldn't be?
6. Is all new test/fixture/seed data synthetic — never a real or realistic-looking record copied in "just for this test"?
7. Does the new field/table/log line need to exist at all, or can the workflow be satisfied with less data (data minimization)?

## Reporting

For each finding: exact location, what data is exposed, to whom (which actor/system), and the minimal fix (usually: log an ID instead of a name/note, or shrink the payload to match the existing minimization pattern) — not a broad "reduce logging" suggestion.
