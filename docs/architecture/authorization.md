# Architecture: Authorization

Primary source: [docs/IDENTITY-AND-ACCESS.md](../IDENTITY-AND-ACCESS.md). Full
documentation: [../security/authorization.md](../security/authorization.md).

## The contracts

```ts
interface Access {
  permit(actor, action: Permission, patientId?): Promise<void>;
  allowed(actor, patientId, write?): Promise<boolean>;
  check(actor, patientId, write?): Promise<void>;
  grant(actor, patientId, target, role, expires, reason?): Promise<void>;
  block(actor, patientId, blocked): Promise<void>;
  context?(actor, patientId?): Promise<{permissions, unitId, name}>;
  eligible?(actor, targetId, patientId, action): Promise<boolean>;
}
interface Workforce {
  current(actor); assignments(actor); staff(actor);
  create(actor, input); update(actor, id, version, input);
  units: {id, tenant, name}[];
}
```

(`packages/contracts.ts`.) `plugins/access.ts` is the legacy/development
policy; `plugins/access-clinic.ts` is the strict clinic policy (unit/
assignment/action/patient-relationship checks); `plugins/access-review.ts`
implements `AccessReview` (manual audit review, emergency access, protected-
identity toggling).

## The permission set

Exactly these 24 permissions exist (`Permission` type, `packages/contracts.ts`):

```
coordination.read, coordination.write, coordination.manage,
coordination.export, coordination.billing, coordination.discharge,
modules.manage, chart.read, chart.export, patient.register, record.write,
note.sign, medication.write, medication.reconcile, lab.order, lab.receive,
lab.review, schedule.write, task.write, ai.use, access.manage,
access.emergency, patient.protected, workforce.manage, integration.manage,
audit.review
```

Every clinical mutation, export, and AI call checks a specific permission
from this list — there is no unconditional "authenticated = authorized"
fallback in the strict policy. Permission-to-role mapping itself is a
clinical-governance decision, not an engineering default — see
[../clinical/clinical-governance.md](../clinical/clinical-governance.md) and
[.claude/rules/clinical-governance.md](../../.claude/rules/clinical-governance.md).

## Architectural invariant

Server-side authorization never trusts a client-supplied organization, unit,
or patient ID as proof of access — every check re-derives the actor's actual
current assignment from the store. See
[../security/tenant-isolation.md](../security/tenant-isolation.md) and
[../security/access-control.md](../security/access-control.md).
