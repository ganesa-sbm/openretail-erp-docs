# TASK-047 — Supplier catalogue and price-list import

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.purchasing` |
| Traces to | FR-341..FR-344 |
| Depends on | TASK-040 |
| Size | M |
| Primary role | Core backend |

## Goal

Load supplier catalogues and price updates without retyping thousands of rows.

## Scope

**In:** CSV/XLSX import with column mapping profiles per supplier, validation and preview, create-vs-update rules, supplier item code mapping, price-change report, scheduled import from a watched folder.

**Out:** EDI.

## Implementation notes

- Imports are staged and previewed with a diff before commit — never blind-applied.
- Per-row errors are reported with the row number; valid rows may still be committed at the user's choice.
- A price change is a price-list publication (TASK-026), audited, not an in-place silent update.

## Acceptance criteria

1. Importing 10,000 rows completes within the documented budget and produces an accurate change preview.
2. Malformed rows are reported precisely and do not abort the import.
3. Re-importing the same file is idempotent.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
