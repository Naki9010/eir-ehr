# Feature Documentation

Each file follows the required template (Purpose, User Workflow,
Architecture, Data Model, API, Authorization, Security, Privacy, Clinical
Safety, Audit, Interoperability, Error Handling, Concurrency, Testing, Known
Limitations, Future Improvements). Every fact is sourced from the flat
`docs/*.md` files and source code — see each doc's "Primary source" line.

## Documented features

| Feature | Status |
| --- | --- |
| [patient-registration-and-chart.md](patient-registration-and-chart.md) | Implemented, tested |
| [medication-reconciliation.md](medication-reconciliation.md) | Implemented, tested |
| [laboratory-integration.md](laboratory-integration.md) | Implemented, tested against local sandbox; no external lab connected |
| [follow-up.md](follow-up.md) | Implemented, tested |
| [deterioration-monitoring.md](deterioration-monitoring.md) | Implemented, tested; off by default, clinical activation blocked in the clinic example |
| [care-team-scheduling.md](care-team-scheduling.md) | Implemented, tested |
| [samverkan-care-coordination.md](samverkan-care-coordination.md) | Implemented, tested; single-tenant demonstration, not regional federation |
| [ai-assisted-documentation.md](ai-assisted-documentation.md) | Implemented, tested (transport-level); no clinical-quality evaluation performed |

## Not yet substantial enough for a standalone feature doc

FHIR export is documented in depth under
[../interoperability/fhir/](../interoperability/fhir/README.md) rather than
here, since it is a projection of the features above rather than an
independent user-facing workflow. Terminology (ICD-10-SE) is documented
under [../clinical/diagnoses.md](../clinical/diagnoses.md) for the same
reason — it's a service other features depend on, not a standalone
end-user workflow.
