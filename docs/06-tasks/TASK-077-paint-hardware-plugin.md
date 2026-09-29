# TASK-077 — Paint and hardware vertical plugin

| Field | Value |
| --- | --- |
| Phase | 3 |
| Module(s) | `erp.plugin.paint` |
| Traces to | BR-067, FR-610..FR-614 |
| Depends on | TASK-024 |
| Size | L |
| Primary role | Core backend |

## Goal

Serve paint and hardware counters: shade cards, tinting machines, and the fastener/fittings long tail.

## Scope

**In:** Shade catalogue and formula import, tinting-machine driver via `PeripheralDriver`, base/colorant stock view, shade reprint labels, bulk-to-loose sale helpers, fitment/compatibility attributes for hardware.

**Out:** Colour matching from spectrophotometers.

## Implementation notes

- Tinting uses core production (TASK-023/024); the plugin adds the catalogue and the machine driver only.
- Machine failure must degrade to manual tinting with the formula printed — the counter keeps selling.
- Shade formulas are versioned; a repeat customer's previous shade must be reproducible exactly.

## Acceptance criteria

1. Selecting a shade dispenses via the machine, consumes colorant stock and prints a shade label.
2. With the machine offline, the formula prints and the sale completes with manual confirmation.
3. Re-ordering a customer's previous shade reproduces the exact formula version used.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
