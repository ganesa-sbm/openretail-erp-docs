# TASK-042 — Goods receipt notes

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.purchasing, erp.inventory` |
| Traces to | FR-313..FR-319, BR-038 |
| Depends on | TASK-041 |
| Size | L |
| Primary role | Core backend |

## Goal

Receive stock accurately, with batches, costs and rejections, because everything downstream inherits these numbers.

## Scope

**In:** GRN against PO or direct, quantity/quality check, batch and expiry capture, barcode label trigger, rejection and return-to-supplier, receipt costing, GRN printing.

**Out:** Quality-management workflows beyond accept/reject.

## Implementation notes

- The GRN posts the stock movements and the goods-received-not-invoiced (GRNI) accrual — stock arrives before the invoice does and the books must reflect that.
- Batch/expiry capture is mandatory for batch-tracked items and blocks completion if missing.
- Receipt cost is provisional until the invoice matches; landed costs adjust it (TASK-044, TASK-045).

## Acceptance criteria

1. A receipt against a PO updates stock, pending PO quantity and the GRNI account correctly.
2. Receiving a batch-tracked item without expiry is blocked with a clear message.
3. A direct receipt with no PO is possible with a permission and appears in the exception report.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
