# TASK-058 — JavaFX renderer for payload-driven screens

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.client.fx` |
| Traces to | FR-507..FR-511, NFR-003 |
| Depends on | TASK-057 |
| Size | L |
| Primary role | UI engineer |

## Goal

Render configured layouts in the desktop client with production-grade performance and accessibility.

## Scope

**In:** Component implementations for the full catalogue, data binding to contexts, virtualised grids, validation display, keyboard navigation from `tabIndex`, theming, i18n.

**Out:** Vaadin renderer (TASK-072).

## Implementation notes

- Rendering must be incremental: only changed bindings update; no full rebuilds on keystrokes.
- Grids are virtualised — 10,000 rows must scroll smoothly on the minimum hardware profile.
- Focus order, labels and contrast come from the payload and must satisfy the accessibility check (NFR-014).

## Acceptance criteria

1. Rendering the largest core layout stays within the documented frame budget (measured, in CI).
2. A TestFX suite drives a rendered layout entirely by keyboard.
3. Identical payloads render equivalently in FX and Vaadin for the shared component set (cross-renderer golden tests).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
