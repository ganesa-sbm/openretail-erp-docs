# TASK-072 — Vaadin web client (Phase 2)

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.client.web` |
| Traces to | FR-582..FR-586, DEP-02 |
| Depends on | TASK-069 |
| Size | XL |
| Primary role | Web UI engineer |

## Goal

Give managers and accountants browser access over the LAN without installing anything.

## Scope

**In:** Vaadin application shell, navigation and security integration, payload-driven renderer for the shared component set, back-office screens (masters, purchasing, accounting, reports, dashboards), responsive layout for tablets.

**Out:** POS billing in the browser (explicitly excluded in Phase 2 — latency and peripherals).

## Implementation notes

- The web client consumes the same layouts and the same API as the desktop client; a second implementation of business logic is forbidden.
- Session security matches the desktop: same permissions, same scopes, same audit.
- Documented browser support matrix; no dependency on browser features unavailable on old shop hardware.

## Acceptance criteria

1. Cross-renderer golden tests: shared layouts render equivalently in FX and Vaadin.
2. A manager completes approvals, price changes and report review entirely in the browser.
3. Permission and scope enforcement is identical to the desktop (shared contract test suite).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
