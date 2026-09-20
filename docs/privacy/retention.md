# Retention

Primary source: [docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md)
("Privacy and trust boundaries").

## Current state: no retention policy is implemented or defined

Record and audit retention need distinct, approved schedules; readability
must be preserved across upgrades; exports must be verified for archival
use. **Keeping rows indefinitely, which is what the system currently does,
is not a retention policy** — this is stated explicitly in
docs/REGULATORY-ALIGNMENT.md and repeated here deliberately, because it is
the kind of gap that's easy to silently treat as "handled" simply because
nothing is being deleted.

Swedish patient-record law (Patientdatalagen) generally requires retention
of at least ten years after the last journal entry, with other archival
duties potentially extending it further
([Patientdatalagen 2008:355, ch. 3–4](https://www.riksdagen.se/sv/dokument-och-lagar/dokument/svensk-forfattningssamling/patientdatalag-2008355_sfs-2008-355/)).
Eir's version history supports the *capability* of long retention (nothing
is destructively overwritten) but does not implement a legal archive, a
retention-expiry mechanism, or destruction procedures.

## No deletion endpoint exists (this is deliberate, not a gap to "fix")

There is no delete endpoint anywhere in the API (see
[../clinical/patient-safety.md](../clinical/patient-safety.md)). Erasure
requests must be evaluated by the deploying organization alongside
statutory journal-retention obligations before any deletion capability is
added — CLAUDE.md explicitly prohibits inventing a generic deletion
capability without that legal review (§7, §8).

## Before a real deployment

A named retention schedule (per record type, per audit category, per
backup archive) must be approved by the deploying organization's legal/DPO
function and implemented — this is tracked as open work in
[docs/REGULATORY-ALIGNMENT.md](../REGULATORY-ALIGNMENT.md)'s "Clinical
release gates" table (Privacy and patient rights gate).
