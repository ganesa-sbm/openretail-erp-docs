# TASK-025 — Reorder levels, stock alerts and suggestions

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory` |
| Traces to | FR-158..FR-161, BR-022 |
| Depends on | TASK-019 |
| Size | M |
| Primary role | Core backend |

## Goal

Stop shops running out of fast movers and overstocking slow ones.

## Scope

**In:** Min/max/reorder point per product-store, lead-time and consumption-based suggestion, dead-stock and overstock reports, alert generation feeding notifications, one-click PO from suggestions.

**Out:** Forecasting model (TASK-066).

## Implementation notes

- Suggestions run through the `REORDER_SUGGESTION` decision point so shops can override the policy without a code change (TASK-027).
- Alerts are deduplicated — a shop must not get the same low-stock SMS every hour.
- Suggestions must be explainable: show the consumption, lead time and cover days behind each number.

## Acceptance criteria

1. Low-stock alert fires once per product per configured window and links to a pre-filled PO.
2. Suggested quantities match the documented formula for the fixture dataset.
3. Dead-stock report identifies items with no movement in N days, excluding new items.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
