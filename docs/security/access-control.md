# Access Control (Applied)

Primary source: [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md).

This page is the "in practice" companion to [authorization.md](authorization.md):
concrete examples of how the policy model resolves real requests.

## Registration creates the first relationship

Registering a patient (`POST /api/patients`) records an initial 30-day care
relationship in the registering clinician's active unit — not a permanent
grant, and not access for any other clinician until a further grant exists.

## Signing is author-restricted

Only a draft's author can edit or sign it under clinic policy (see
[../clinical/clinical-notes.md](../clinical/clinical-notes.md)). This is
enforced in the clinical domain service, not merely hidden in the UI.

## Protected identity is a distinct axis from ordinary chart access

`patient.protected` is checked in addition to `chart.read`/`record.write`
wherever a protected patient is involved — directory listing, chart read,
history, change feed, export, and AI. An unauthorized staff member does not
see a redacted patient; they see no patient at all in any of these surfaces.

## Reviewers cannot self-review

Audit review assessments cannot be created by the same actor whose event is
being reviewed — see [../clinical/clinical-review.md](../clinical/clinical-review.md).

## What access control does not yet cover

National protected-person search, safe-contact-channel handling, verified
citizen/proxy access, and country-specific restriction management beyond
the current coarse block/unblock all "fail closed" (deny) rather than being
partially implemented — see [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md)
"Policy".
