# TASK-024 — Paint tinting and loose/bulk handling

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory, erp.plugin.paint` |
| Traces to | FR-153..FR-157, BR-021 |
| Depends on | TASK-023 |
| Size | M |
| Primary role | Core backend |

## Goal

Handle the paint and hardware counter cases: tint a base to a shade at the point of sale, sell cut lengths and dispense from bulk.

## Scope

**In:** Tint formula master (base + colorant quantities), tinting at POS creating a derived saleable item, colorant consumption movements, cut-length/decimal issue, bulk-to-loose conversion.

**Out:** Tinting-machine hardware integration (plugin, TASK-077).

## Implementation notes

- A tinted product is a production event, not a free-text line — colorant stock must decrease.
- Formulas are versioned and costed; price may come from the base plus colorant cost or from a price list (rules, TASK-027).
- Cut lengths must not leave unsellable remnants untracked — remnant handling is explicit.

## Acceptance criteria

1. Selling 4 L of a tinted shade consumes the base and each colorant per formula and posts correct cost.
2. Cutting 3.5 m from a 10 m roll leaves 6.5 m on hand with the correct cost.
3. The whole flow completes within the POS latency budget at a busy counter.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
