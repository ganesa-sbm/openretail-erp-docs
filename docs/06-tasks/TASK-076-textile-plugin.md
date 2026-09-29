# TASK-076 — Textile vertical plugin

| Field | Value |
| --- | --- |
| Phase | 3 |
| Module(s) | `erp.plugin.textile` |
| Traces to | BR-066, FR-605..FR-609 |
| Depends on | TASK-073 |
| Size | L |
| Primary role | Core backend |

## Goal

Support how textile shops actually sell: size/colour matrices, bulk barcoding and cut lengths.

## Scope

**In:** Size/colour matrix entry and grid billing, design/style codes, matrix purchase and stock views, roll/piece tracking with cut lengths, season and collection attributes, matrix stock report.

**Out:** Garment manufacturing.

## Implementation notes

- Matrix screens are layout patches plus a grid component, not a replacement POS.
- Cut-length handling reuses core loose-quantity support (TASK-015/024) rather than duplicating it.
- Matrix stock views read projections; a 20×15 matrix must render instantly.

## Acceptance criteria

1. Entering a purchase as a size×colour matrix creates the correct variant-level movements.
2. Matrix stock view for a style shows correct quantities and totals on the Large fixture within budget.
3. Uninstalling the plugin leaves core inventory and existing data fully functional.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
