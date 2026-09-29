# TASK-020 — Inventory valuation: weighted average and FIFO

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory, erp.accounting` |
| Traces to | FR-132..FR-136, BR-015 |
| Depends on | TASK-019 |
| Size | L |
| Primary role | Accounting specialist |

## Goal

Value stock consistently so COGS, margin and the balance sheet agree.

## Scope

**In:** Per-product valuation method, moving weighted-average cost maintenance, FIFO layer tracking, landed-cost absorption, revaluation, COGS computation feeding posting, valuation report.

**Out:** Posting engine (TASK-050).

## Implementation notes

- Cost is recomputed on receipt-side movements only; issues consume at the prevailing cost/layer.
- Back-dated receipts are the hard case: define and implement one policy (recompute forward with an audited revaluation entry) and document it.
- Valuation must tie to the inventory control account at all times — a reconciliation job proves it.

## Acceptance criteria

1. Stock value report equals the inventory control account balance for the Large fixture, daily, in CI.
2. FIFO layers are consumed in order and the report can show remaining layers with their costs.
3. A back-dated receipt produces an auditable revaluation rather than silently changing history.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
