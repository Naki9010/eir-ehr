# Laboratory Results

Primary source: [docs/MEDICATIONS-AND-RESULTS.md](../MEDICATIONS-AND-RESULTS.md),
[docs/INTEGRATIONS.md](../INTEGRATIONS.md), [docs/FOLLOW-UP.md](../FOLLOW-UP.md).

## Lifecycle

```
Order → (optional connector dispatch) → Report received → Review → (Correction reopens review)
```

1. **Order**: `POST /api/patients/:id/lab-orders` — `{clientId, encounterId,
   test, question, specimen, assigneeId, due, priority}`, optionally
   `connectorId` for a configured lab connector (otherwise local/manual).
   The order and its owned follow-up task commit in the same transaction.
2. **Report**: `POST /lab-orders/:id/receive` (manual entry) or, for a
   connected lab, `POST /integrations/:connectorId/results` (machine
   credential, outside `/api`). Receipt (`202`) means **durably received**,
   not clinically applied or reviewed — see
   [clinical/patient-safety.md](patient-safety.md#received--reviewed--acted-on).
   Up to 30 analytes per report; each has `{name, value, unit, reference,
   flag}` where `flag` is `unknown | normal | high | low | critical` —
   **source-supplied only**, never inferred or guessed by Eir.
3. **Review**: `POST /lab-orders/:id/review` — only the assigned reviewer
   (or someone who has taken ownership via task handover) can sign this
   application attestation. A `critical` flag requires an explicit,
   separate acknowledgement (`criticalAcknowledged`) before review can
   complete.
4. **Correction**: a corrected report is a **complete replacement**, not a
   partial patch. The previous report and its review remain intact and
   visible as replaced; the order returns to unreviewed; the linked task
   reopens **even if already completed**.

## The safety guard specific to lab tasks

`CareTeam.createLinkedTask`/`syncLinkedTask` mark a task as lab-linked.
Generic task completion or cancellation **cannot** bypass report review —
this is enforced in the care-team provider contract itself, not only in the
UI. See [docs/MEDICATIONS-AND-RESULTS.md](../MEDICATIONS-AND-RESULTS.md)
"Replacement Contracts".

## Connected-lab integration (optional)

`eir.lab.v1` is Eir's own JSON-over-HTTPS protocol — explicitly not HL7 v2,
not a FHIR implementation guide, and not an Inera contract. See
[../interoperability/integrations/integration-catalog.md](../interoperability/integrations/integration-catalog.md)
and [../features/laboratory-integration.md](../features/laboratory-integration.md).

## Not implemented

No coded laboratory catalogue (NPU/LOINC), no automatic critical-result
paging/escalation daemon, no specimen collection/label/accession workflow,
no contracted Swedish laboratory connection.
