# Accessibility

Primary source: [docs/ARCHITECTURE.md](../ARCHITECTURE.md) ("UX Contract"),
[docs/PLAN.md](../PLAN.md).

## What is implemented

Swedish labels, responsive unframed sections, keyboard-accessible native
HTML controls, two interchangeable chart views exercised at desktop and
mobile widths, explicit empty/error states, and reduced-motion support
(docs/ARCHITECTURE.md).

## What is explicitly not yet verified

No task-based accessibility testing to a named standard has been completed.
[docs/PLAN.md](../PLAN.md) sets **WCAG 2.2 AA task-based accessibility
testing** as a named acceptance gate for M2 ("complete consultation")
before any clinical pilot — it is a target, not a completed audit. There is
no automated axe-core/pa11y-style accessibility test in the current test
suite (see [../testing/e2e-tests.md](../testing/e2e-tests.md) for what the
Playwright suite does check: layout at desktop/mobile widths, not WCAG
conformance).

## Explicit non-negotiable

Critical clinical information is never communicated using color alone
(CLAUDE.md §36). This is a design constraint the existing renderers follow
(e.g. critical lab flags, unresolved alerts) but it has not been
independently verified against assistive technology.

## Before a clinical pilot

Validate workflows with clinicians, reception staff, and patients using
keyboard-only and screen-reader navigation, per
[docs/ARCHITECTURE.md](../ARCHITECTURE.md) "UX Contract" — this has not yet
happened.
