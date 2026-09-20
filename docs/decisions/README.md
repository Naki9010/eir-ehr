# Architecture Decision Records

Use [ADR-0001-template.md](ADR-0001-template.md) for new decisions. Number
sequentially; never renumber or delete a superseded ADR — mark its status
`Superseded` and link to the replacement instead.

## Index

| ADR | Title | Status | Scope |
| --- | --- | --- | --- |
| [ADR-0001](ADR-0001-plugin-runtime.md) | Plugin runtime with declared service contracts | Accepted | Architecture |
| [ADR-0002](ADR-0002-fhir-projection-not-server.md) | FHIR as an export projection, not a full FHIR server | Accepted | Architecture, Interoperability |
| [ADR-0003](ADR-0003-async-storage-contract-v2.md) | Asynchronous storage/service contract (runtime API v2) | Accepted | Architecture, Database |
| [ADR-0004](ADR-0004-note-immutability-via-db-trigger.md) | Enforce signed-note immutability at the database layer | Accepted | Clinical Safety, Database |
| [ADR-0005](ADR-0005-no-patient-merge.md) | Do not implement patient merge/split until a dedicated design exists | Accepted | Clinical Safety |
| [ADR-0006](ADR-0006-postgres-rls-tenant-isolation.md) | Enforce tenant isolation via PostgreSQL role-bound RLS | Accepted | Security |
| [ADR-0007](ADR-0007-no-untrusted-plugin-sandbox.md) | Do not claim plugin sandboxing; document the trust boundary instead | Accepted | Security |

These ADRs were reconstructed from the technical decisions already visible
in the flat `docs/*.md` files and source code — they document decisions
that were clearly and deliberately made (evidenced by explicit statements
like "this is not X" or "deliberately Y" in the source docs), not decisions
this documentation effort invented. Going forward, propose new ADRs at the
time a real architectural decision is made, not retroactively.
