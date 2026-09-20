# Regulatory Evidence Rules — Eir EHR

Global authority: [CLAUDE.md](../../CLAUDE.md) §7, §21, §50. This file gives the `regulatory-evidence` agent the detailed shape. Primary source: `docs/REGULATORY-ALIGNMENT.md` — always re-read it directly; it is a living evidence brief, not a static reference, and this file only indexes it.

## What the evidence brief actually says (as of its last review date in the doc)

> "Eir is an open-source EHR under active development... Several implemented controls support Swedish and EU regulatory requirements. The project has not established complete regulatory compliance, clinical validation, national-service approval or readiness for real patient use. This document is an evidence map, not a declaration of conformity, certification, legal opinion or regulator endorsement."

Treat that sentence as the standing baseline for every regulatory-adjacent statement this project makes, in code comments, docs, or conversation.

## The six domains and what triggers each

| Domain | Governing text (cited in docs/REGULATORY-ALIGNMENT.md) | What in this repo would newly implicate it |
|---|---|---|
| Patient records/access | Patientdatalagen 2008:355 ch. 3–4 | Note amendment/correction changes, retention/deletion behavior, archive claims |
| Information security/supervision | HSLF-FS 2016:40 incl. 2025:57 | Authorization model changes, access-log retention window changes |
| Personal data | GDPR Art. 6/9, 25, 32–35, Ch. V | New export path, new third-party integration, new hosting region, new data category collected |
| Product qualification | MDR/IVDR, Swedish NMI (HSLF-FS 2022:42) | New AI/CDS capability, especially anything closer to autonomous action or diagnostic suggestion |
| EU interoperability | EHDS (2029/2031 milestones) | Any claim that FHIR export satisfies EHDS obligations (it doesn't, by itself) |
| AI regulation | AI Act | Any new AI feature, especially one presentable as clinical decision support |

## Non-negotiables

- "Implemented" (a working repository control with developer test evidence) is explicitly **not** the same as "independently validated, operationally adopted, or legally sufficient" — the evidence brief states this distinction outright for its entire controls table. Never collapse it.
- Manual review functionality (e.g. the audit review UI) is not evidence that reviews are actually staffed or performed — don't let a technical capability stand in for an organizational commitment.
- A plugin label or a human-in-the-loop step does not settle a medical-device classification question by itself.
- No deployment-specific DPIA, DPA, or transfer assessment exists yet for this project — never imply one has been done.
- Neither EHDS nor AI Act conformity is claimed anywhere today — don't let new work drift into implying otherwise through hopeful phrasing.

## What to actually do when a change touches one of these domains

1. Name which domain(s) from the table above are implicated, and why.
2. Point to the exact existing docs/REGULATORY-ALIGNMENT.md row/section covering it (or note that this is genuinely new ground the document doesn't yet cover).
3. State plainly what regulatory/legal assessment this change would newly require before real-data use — do not perform that assessment yourself.
4. If the change is CRITICAL-risk under CLAUDE.md §60 (autonomous AI action, automatic critical-result closure, cross-tenant/border data flow), stop and flag it as requiring the "accountable... regulatory leads" decision docs/REGULATORY-ALIGNMENT.md's executive conclusion calls for — do not implement it as a fait accompli.

## Relationship to other agents

`clinical-governance` owns *clinical* policy gaps (thresholds, terminology, escalation rules) — this agent owns *legal/regulatory* evidence gaps. They overlap on AI/CDS features and should be invoked together there. `privacy` owns day-to-day PHI-exposure hygiene; this agent owns the GDPR *legal-basis and assessment* layer above that.
