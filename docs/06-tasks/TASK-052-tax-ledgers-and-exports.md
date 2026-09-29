# TASK-052 — Tax ledgers and statutory exports

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.accounting` |
| Traces to | FR-426..FR-431, BR-047, CMP-03 |
| Depends on | TASK-050 |
| Size | L |
| Primary role | Accounting specialist |

## Goal

Produce the filings the shop must submit, in the formats the portals accept.

## Scope

**In:** Output/input tax ledgers per component, HSN/SAC summary, rate-wise summary, B2B/B2C splits, statutory export formats (CSV/JSON as applicable), exception report for missing identifiers, period figures locked after filing.

**Out:** Direct portal filing (plugin, TASK-078+).

## Implementation notes

- Exports are generated from posted documents only — never from drafts or projections that can change.
- Any document missing mandatory statutory data appears in the exception report before export, not after rejection by the portal.
- A filed period's figures are snapshotted with a hash so a later query reproduces exactly what was filed (CMP-06).

## Acceptance criteria

1. Export for the tax test corpus matches the expected golden files field for field.
2. Documents with missing HSN or customer identifiers are listed before export and block it by default.
3. Re-running an export for a filed period reproduces the identical file (hash equality).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
