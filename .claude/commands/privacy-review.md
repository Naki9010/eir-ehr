---
description: Focused privacy/PHI-exposure review of a change in Eir EHR — logs, telemetry, browser storage, exports, integrations, AI.
argument-hint: [optional path or feature name]
---

Run a privacy review of $ARGUMENTS (default: current diff against `main`) using the **privacy**, **security**, **backend**, and **frontend** agents (CLAUDE.md §91).

Check specifically:

- **PHI exposure in logs**: any new log line, error message, or telemetry event carrying a name, personnummer, clinical note text, lab result, medication detail, token, or credential? (The server today logs none of this — treat that as the bar.)
- **Browser storage**: any patient data or bearer token written to `localStorage`/`sessionStorage`/IndexedDB?
- **URLs**: any identifier or token appearing in a query string or path that shouldn't be there?
- **Exports**: does a new export path (FHIR, CSV/PDF, audit paging) return more than the destination/actor's authorized visibility allows (draft/proposal exclusion for patient/proxy, `sam*` exclusion from chart/FHIR)?
- **Integrations**: does a new connector/notification payload minimize patient data the way `OrderMessage` and the deterioration risk-snapshot already do, rather than forwarding the full chart?
- **AI**: is the AI input still a bounded, authorized copy of context, not an unrestricted chart credential?
- **Third-party services**: does anything send patient data to a non-loopback, non-explicitly-configured destination?
- **Test/dev data**: any real or realistic-looking patient data introduced into fixtures, screenshots, or docs?
- **Retention/minimization**: does a new field or table need to exist at all, or can the workflow be satisfied with less data?

Report using CLAUDE.md §85 format. Do not modify code unless explicitly asked.
