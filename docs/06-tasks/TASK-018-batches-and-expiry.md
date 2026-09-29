# TASK-018 — Batches, expiry and FEFO

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory` |
| Traces to | FR-118..FR-124, BR-007..BR-010 |
| Depends on | TASK-015 |
| Size | L |
| Primary role | Core backend |

## Goal

Track perishable and lot-controlled goods so shops stop selling expired stock and can trace a recall.

## Scope

**In:** Batch master (batch no, mfg/expiry, supplier, cost), batch-tracked flag per product, FEFO allocation, expiry alerts, blocked batches, batch traceability report.

**Out:** Notifications delivery (TASK-064).

## Implementation notes

- Batch tracking is per product; non-tracked products must not pay any cost for the feature.
- FEFO allocation is deterministic and overridable with a permission and an audit entry.
- Selling an expired or blocked batch is refused; override requires approval and is audited.
- Traceability answers: where did this batch come from, and which invoices contain it.

## Acceptance criteria

1. FEFO picks the earliest-expiry available batch across locations within the allowed scope.
2. Expired stock is excluded from sellable on-hand but remains visible in inventory reports.
3. Batch traceability for a given batch returns receipts and sales within the documented budget.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
