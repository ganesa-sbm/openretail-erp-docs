# TASK-065 — Analytics projections and dashboards

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.analytics` |
| Traces to | FR-544..FR-550, BR-055 |
| Depends on | TASK-019 |
| Size | L |
| Primary role | Core backend |

## Goal

Give owners and managers the numbers they check every day, fast, even on a counter machine.

## Scope

**In:** Incremental projections (sales by day/hour/item/category/counter/staff, margin, stock value and ageing, purchases, receivables/payables, cash), dashboard definitions (Today, Sales, Inventory, Purchasing, Cash & Books, Staff, Health), drill-through, date/store filters, export.

**Out:** Forecasting (TASK-066); external BI.

## Implementation notes

- Projections update incrementally from domain events and are fully rebuildable — never the only copy of anything.
- Dashboards must load within the documented budget from projections only; no dashboard query touches raw movement tables.
- Every tile drills through to a report and then to documents — trust requires verifiability.

## Acceptance criteria

1. Dashboard load P95 within budget on the Large fixture on the minimum hardware profile.
2. A full projection rebuild reproduces identical figures (hash comparison).
3. Every tile has a working drill-through path to source documents.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
