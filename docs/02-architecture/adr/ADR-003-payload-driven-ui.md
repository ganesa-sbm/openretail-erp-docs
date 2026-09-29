# ADR-003 — Payload-driven UI over a fixed component catalogue

Status: ACCEPTED · Date: 2026-09-29 · Drivers: BR-050, RSK-5

## Context

Verticals need different fields and different screen arrangements (textile needs size/colour matrix entry; paint needs shade code and tint; hardware needs pack conversions). Shipping a build per customer is unsustainable; a fully generic form builder becomes a slow, unbounded product of its own.

## Decision

Screens are rendered from a **layout payload** (JSON, schema-validated, versioned) that composes a **fixed catalogue of components** (`textField`, `numberField`, `lookup`, `grid`, `matrixGrid`, `tabs`, `section`, `actionBar`, `summaryPanel`, …). The payload can set field order, visibility, labels, defaults, presentation validation, keyboard shortcuts and data bindings, but it cannot define new widgets or embed logic — behaviour comes from named actions implemented in code or from rules.

The POS billing screen is explicitly exempt from full genericity: it is hand-built for latency (NFR-001/2) and exposes only a constrained set of configurable slots.

## Consequences

**Positive**: per-tenant customisation without a build; plugins contribute layouts; A/B of field arrangement is trivial; the same payload can drive JavaFX and Vaadin renderers.

**Negative**: a schema, validator, editor and versioning story are required; poorly authored layouts can degrade UX; debugging is indirect.

**Guardrails**: JSON schema validation on save; a performance budget per screen (≤ 120 bound components, first paint ≤ 300 ms); layouts are versioned and rollback-able; a default layout always ships and can be restored in one click.
