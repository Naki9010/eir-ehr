---
name: hl7-review
description: Check any request or claim involving "HL7" against the actual state of Eir EHR — there is no HL7 integration in this repository today. Use whenever HL7 is mentioned, before writing any HL7-related code, comment, or documentation.
---

# HL7 Review

## Ground truth

Eir EHR has no HL7 v2/v3 parsing, ACK/NACK handling, segment mapping, or message types anywhere in the codebase. The lab integration protocol (`eir.lab.v1`, in `plugins/integrations.ts` / `packages/integrations.ts`) and the notification protocol (`eir.notification.v1`) are both Eir's own JSON-over-HTTPS designs. [docs/INTEGRATIONS.md](../../../docs/INTEGRATIONS.md) states this explicitly: *"It is not HL7 v2, a FHIR implementation guide or an Inera contract."*

## What to do when "HL7" comes up

1. **Ask/confirm what's actually meant.** In practice, "HL7" almost always refers to the existing lab order/result flow. Point to `.claude/agents/integration.md` and `plugins/integrations.ts` for that.
2. **If genuine HL7 v2/v3 work is actually wanted**, this is new, CRITICAL-risk work (CLAUDE.md §60) requiring a real trading-partner specification (segments, version, encoding) that cannot be guessed from general HL7 knowledge. Reuse the existing integration architecture's discipline (durable queues, lease/retry, idempotent message IDs, machine-scoped credentials, atomic apply-in-transaction) rather than inventing a parallel mechanism.
3. **Never write or leave in place** a comment, docstring, UI label, or doc sentence implying HL7 support exists. If you find one, that's a finding to report and fix.

## Before writing "HL7 works" or similar

That claim is currently always false for this repository. Report instead: "No HL7 integration exists; the existing `eir.lab.v1` protocol was verified instead," or, if new HL7 work was actually built, cite the specific partner spec and validator used.
