# TASK-060 — Report model, data sources and renderers

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.reporting` |
| Traces to | FR-517..FR-522, BR-052 |
| Depends on | TASK-005 |
| Size | L |
| Primary role | Core backend |

## Goal

Provide the engine behind every report, document and label.

## Scope

**In:** Report definition model (bands, groups, aggregates, parameters), named `ReportDataSource` registry with permissions, streaming execution, renderers for PDF, CSV, XLSX, ESC/POS and label languages, pagination, localisation.

**Out:** The drag-and-drop designer (TASK-061).

## Implementation notes

- Execution streams via cursors — a report must never materialise a full result set; a shop counter has 4 GB of RAM.
- Data sources read projections, declare parameters and a required permission, and apply the caller's data scope.
- Rendering is deterministic: the same definition and data produce byte-identical PDF output (golden-file testing).

## Acceptance criteria

1. A 100,000-row report exports to CSV within the documented budget with bounded memory (heap assertion).
2. Golden-file PDF comparison passes for the built-in templates.
3. A data source invoked without its permission is refused, and scope filtering is applied (contract test).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
