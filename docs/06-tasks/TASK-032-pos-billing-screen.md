# TASK-032 — POS screen: keyboard-first billing UI

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.client.fx` |
| Traces to | FR-217..FR-226, NFR-001, NFR-012 |
| Depends on | TASK-031 |
| Size | XL |
| Primary role | UI engineer |

## Goal

Build the screen the business lives on — fast, keyboard-driven, forgiving of hardware and network faults.

## Scope

**In:** Item entry by scan/code/search, line grid with inline edits, quantity/discount shortcuts, customer attach, totals panel, tender dialog, hold/recall, offline indicator, error toasts, full keyboard map with an on-screen cheat sheet.

**Out:** Touch-optimised layout (later), mobile POS.

## Implementation notes

- Nothing blocks the FX application thread: all lookups, rule evaluations and prints are async with an explicit pending state.
- Every action has a key binding; the mouse must be optional for a complete sale.
- The screen must render and remain usable at 1366×768 and on the minimum hardware profile.
- Connectivity state is always visible and changes within 2 s of loss/restore.

## Acceptance criteria

1. Scan-to-line P95 within the documented budget; finalise-to-print within the bill budget (NFR-001, NFR-002) on the minimum profile.
2. A complete sale (5 lines, split tender, print) is performed with keyboard only in a TestFX scenario.
3. Pulling the network mid-bill leaves the bill intact and switches to local mode without losing a keystroke.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
