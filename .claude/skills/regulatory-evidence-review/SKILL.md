---
name: regulatory-evidence-review
description: Check whether an Eir EHR change shifts the project's regulatory footprint (Patientdatalagen, HSLF-FS, GDPR, MDR/IVDR, EHDS, AI Act) against docs/REGULATORY-ALIGNMENT.md. Use for AI-autonomy changes, new exports/integrations, or anything that could read as clinical decision support.
---

# Regulatory Evidence Review

See `.claude/rules/regulatory-evidence.md` for the full domain table this skill draws from, and re-read `docs/REGULATORY-ALIGNMENT.md` directly — it's a living evidence brief, not a static list.

## Fast checklist

1. Does this change touch **patient records/retention/correction** behavior? → Patientdatalagen 2008:355 ch. 3–4.
2. Does it change **authorization or access-log retention**? → HSLF-FS 2016:40/2025:57.
3. Does it add a **new data export, third-party integration, hosting region, or data category**? → GDPR Art. 6/9/25/32–35/Ch. V — involve `privacy` too.
4. Does it add or change an **AI/decision-support capability**, especially anything closer to autonomous action? → MDR/IVDR + Swedish NMI (HSLF-FS 2022:42) — involve `clinical-governance` and `ai` too.
5. Does it make or imply a claim about **FHIR/EHDS sufficiency**? → EHDS's phased requirements are not satisfied by a FHIR-shaped export alone.
6. Does it involve a **new AI feature presentable as clinical decision support**? → AI Act applicability depends on intended use and role.

## What to actually output

For each domain touched: name it, cite the specific docs/REGULATORY-ALIGNMENT.md section, and state plainly what assessment this would newly require before real-data use (a DPIA, a device-qualification rationale, a Chapter V transfer assessment, etc.) — **without performing that assessment**. This skill routes; it does not adjudicate.

## Hard stop

If the change involves an **autonomous clinical action without human sign-off, or automatic closure of a critical result**, this is CRITICAL risk (CLAUDE.md §60). Stop and name the required accountable decision-maker per docs/REGULATORY-ALIGNMENT.md's executive conclusion ("appoint a clinical design partner and accountable privacy, security, operations and regulatory leads") rather than implementing it.

## Language discipline

Never write "compliant," "certified," "conformant," or "meets [X] requirements" for any of the six domains without citing the specific evidence — this repo's own documentation is deliberately precise about this distinction; match it.
