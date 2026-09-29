# TASK-016 — Barcodes, labels and item identification

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory` |
| Traces to | FR-113..FR-116, BR-006 |
| Depends on | TASK-014 |
| Size | M |
| Primary role | Core backend |

## Goal

Make scanning work for every way shops identify goods, including weight-embedded and self-printed codes.

## Scope

**In:** Multiple barcodes per variant, EAN/UPC/Code128 validation, embedded-weight/price barcode parsing, internal code generation, label template binding, batch label printing.

**Out:** Label renderer internals (TASK-061).

## Implementation notes

- Barcode uniqueness is enforced per tenant; duplicate creation offline is a known conflict case (TASK-068).
- Embedded-weight barcode patterns are configurable (prefix, item digits, weight digits, check digit).
- Lookup by barcode must be a single indexed query — it is on the POS hot path.

## Acceptance criteria

1. Scanning a weight-embedded barcode adds the correct item and quantity in one action.
2. Barcode lookup P95 under 50 ms at 500k barcodes.
3. Printing labels for a goods receipt produces one label per unit with batch and price.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
