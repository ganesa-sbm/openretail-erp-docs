# TASK-026 — Price lists and price resolution

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.pricing` |
| Traces to | FR-162..FR-168, BR-023 |
| Depends on | TASK-017 |
| Size | M |
| Primary role | Core backend |

## Goal

Resolve one unambiguous, explainable price for every line, across customer groups, stores and time.

## Scope

**In:** Price list master, price by variant/UOM/customer group/store, validity windows, MRP vs selling price, cost-plus pricing, price history, resolution service with an explanation trail.

**Out:** Discount rules (TASK-029).

## Implementation notes

- Resolution order is explicit and deterministic (customer-specific > group > store > default); ties are impossible by construction.
- The service returns the price plus the reason chain — the cashier must be able to answer 'why this price'.
- Resolution is on the POS hot path: cached with invalidation on price-list publication.

## Acceptance criteria

1. The same inputs always produce the same price and the same explanation.
2. P95 resolution under the documented budget with 100k variants and 5 price lists.
3. A published price list takes effect at all counters within the documented propagation time, including after reconnect.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
