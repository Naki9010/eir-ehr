# Evidence

Primary source: [docs/ARCHITECTURE.md](../ARCHITECTURE.md) ("AI Native
Workflow").

## Source-quotation validation

The source quotation validator rejects invented references and quotes — a
proposal's citations must correspond to actual record IDs/versions in the
authorized evidence snapshot. This is what CLAUDE.md §45's "AI must not
silently... fabricate citations" is enforced by, not merely stated as a
policy.

## What evidence includes as of the medication/results release

AI evidence now includes documented medication status/source/unknown
dosage, lab orders, and **only their latest reports** (superseded reports
are excluded) — a source change invalidates any pending proposal built from
the earlier version (docs/MEDICATIONS-AND-RESULTS.md).

## What evidence validation does NOT prove

Quotation-existence checking establishes that a cited record/version really
exists and really contains the quoted text — it does **not** establish that
the AI's surrounding interpretation or synthesis of that evidence is
clinically correct. A model can produce misleading text built from real,
correctly-cited quotes. This distinction is explicit in
docs/OPERATIONS.md's threat table ("Model fabricated source" row) and must
not be elided when describing this control.
