---
name: add-a-country-pack
description: How to add or extend a country/locale pack in Eir EHR (the plugins/country-se.ts / country-eu-local.ts pattern). Use for PLAN.md's country-pack backlog (Estonia/Denmark) or any new national identifier/locale work.
---

# Add A Country Pack

## Ground truth

`plugins/country-se.ts` (Swedish personnummer/samordningsnummer + `sv-SE` locale) is the reference implementation. `plugins/country-eu-local.ts` demonstrates the *same contract* with a different code/locale (e.g. `{"code":"DK","locale":"da-DK"}`) — but docs/PLUGINS.md and docs/PLAN.md are both explicit that this **only proves the plugin is replaceable**. It does **not** implement Danish CPR, Estonian identity, or either country's legal access-rights model. Do not let a change to this pack imply otherwise.

## Steps for a real, new country pack (per PLAN.md P2: "Estonia or Denmark first, chosen with a partner")

1. **Identify the real national identifier scheme** for the target country (format, checksum algorithm, date-of-birth encoding if any) — this must come from an authoritative source, never guessed (CLAUDE.md §7 "never invent... national healthcare requirements").
2. **Implement identifier validation** with the same rigor `personnummer` gets for Sweden: explicit checksum/format tests, not a loose regex.
3. **Keep the same plugin contract** (`provides`/`requires` matching what `country-se.ts` provides) so the rest of the system (clinical, access, terminology) doesn't need to change to consume it.
4. **Pin the terminology and any national IG** the target country actually requires — `plugins/terminology-icd-se.ts` is Sweden-specific; a new country likely needs its own terminology provider, not a reused one.
5. **Write a conformance/migration test suite** specific to this pack (PLAN.md: "conformance suite and migration tests for every pack") — reuse the kernel contract tests, add country-specific identifier/terminology tests on top.
6. **Document exactly what is and isn't implemented**, matching the honesty of the existing `country-eu-local.ts` description — name the specific legal/access requirements *not* covered (proxy rules, retention, consent model) so nobody mistakes the pack for a completed national deployment.
7. **Involve `regulatory-evidence`** — a real country pack changes the regulatory footprint (different national law, potentially different data-residency requirements) and needs its own evidence entry, not a reuse of the Swedish one.

## What NOT to do

- Don't ship a country pack that reuses Swedish validation logic with different error messages — that's the exact anti-pattern docs/PLUGINS.md warns is only a replaceability demo, not a real pack.
- Don't claim a country's national services (professional identity, terminology server, exchange network) are supported because the country/locale plugin exists — those are separate, undone integration efforts per PLAN.md's National Integration Inventory.
