# TASK-015 — Units of measure and conversions

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory` |
| Traces to | FR-109..FR-112, BR-005 |
| Depends on | TASK-014 |
| Size | M |
| Primary role | Core backend |

## Goal

Support shops that buy in one unit and sell in another, including fractional quantities.

## Scope

**In:** UOM master, UOM classes, conversion factors (fixed and per-product), purchase/sale/stock UOM per product, loose-quantity rules, rounding.

**Out:** Weighing-scale integration (TASK-039).

## Implementation notes

- Conversions are exact `BigDecimal` factors; converting and converting back must not drift (property test).
- Each product declares stock UOM; all movements are stored in stock UOM with the transacted UOM recorded for the trail.
- Loose items (metres, litres, kg) allow 3-decimal quantities; discrete items reject fractions.

## Acceptance criteria

1. Buying a 12-piece box and selling pieces produces correct on-hand and correct cost per piece.
2. Selling 0.25 of a discrete item is rejected with a clear message; 0.250 kg is accepted.
3. Round-trip conversion property test passes over random factors.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
