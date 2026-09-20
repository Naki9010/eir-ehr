# Human Review

Primary source: [docs/API.md](../API.md), [docs/ARCHITECTURE.md](../ARCHITECTURE.md).

## The review call

`POST /proposals/:id/review` — `{version, decision: "accept" | "reject",
text?}`. A clinician may edit the proposed text before accepting
(`text?`), and acceptance links to a **new draft note** via `noteId` — the
proposal itself is never itself the clinical record.

## Why "draft," specifically, matters

Accepting a proposal does not sign anything. The resulting note goes
through the exact same draft → sign lifecycle as any manually written note
(see [../clinical/clinical-notes.md](../clinical/clinical-notes.md)) — a
second, distinct clinician action (signing) is required before it becomes
part of the immutable signed record. This two-step design is what keeps AI
"assistive" rather than "authoritative" in a structural sense, not just a
policy statement.

## What cannot happen

A rejected proposal cannot later be silently accepted; an accepted proposal
cannot be re-reviewed with a different decision (see [validation.md](validation.md)
"Replay protection"). There is no auto-accept path anywhere in the API —
every acceptance requires an authenticated `record.write`/`ai.use`-permitted
actor's explicit call.
