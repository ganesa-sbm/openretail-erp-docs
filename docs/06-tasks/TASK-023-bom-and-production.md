# TASK-023 — Bills of material, assembly and production

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory` |
| Traces to | FR-146..FR-152, BR-018..BR-020 |
| Depends on | TASK-020 |
| Size | L |
| Primary role | Core backend |

## Goal

Support the light manufacturing and repacking these shops actually do: kits, assembly, bulk-to-retail packing and simple production orders.

## Scope

**In:** BOM master with versions, component substitution, production order, issue/receipt movements, by-products and wastage, yield variance, production costing.

**Out:** Full MRP, scheduling, shop-floor control.

## Implementation notes

- Production consumes components and produces output in one transaction; cost of output = consumed cost + declared overheads.
- Yield variance is recorded explicitly, not absorbed silently, so shrinkage is visible.
- BOMs are versioned; an open production order keeps the BOM version it started with.

## Acceptance criteria

1. Producing 10 units of a kit consumes the BOM quantities and produces correctly costed output.
2. Wastage and by-products post their own movements and appear in the yield report.
3. Changing a BOM does not alter previously completed production orders.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
