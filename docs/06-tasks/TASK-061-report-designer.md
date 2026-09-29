# TASK-061 — Drag-and-drop report designer

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.client.fx` |
| Traces to | FR-523..FR-528, BR-052 |
| Depends on | TASK-060 |
| Size | L |
| Primary role | UI engineer |

## Goal

Let shops build and adjust their own reports and document formats.

## Scope

**In:** Band-based canvas, field palette from the selected data source, grouping and aggregate builder, formatting (money/qty/date/percent), page setup for A4/thermal/label, preview with real data, template versioning and publish, built-in templates as read-only starting points.

**Out:** Scripting inside reports (deliberately excluded).

## Implementation notes

- No free-form scripting in templates — formatting and aggregation are declarative, which keeps templates safe, portable and fast.
- Preview must use realistic data volumes; three-row previews hide every layout bug.
- Templates are versioned and audited like layouts and rules.

## Acceptance criteria

1. A user builds a grouped, totalled report from an existing data source with no code and publishes it.
2. Preview matches printed output for A4 and 80 mm thermal.
3. Rollback restores the previous template version immediately.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
