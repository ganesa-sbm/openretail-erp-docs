# TASK-086 — Data migration toolkit for onboarding shops

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `tools` |
| Traces to | BR-074, FR-625..FR-628 |
| Depends on | TASK-047 |
| Size | L |
| Primary role | Implementation consultant |

## Goal

Get an existing shop's data in accurately — the main determinant of whether a go-live succeeds.

## Scope

**In:** Import templates (items, variants, barcodes, parties, opening stock with batches and costs, opening balances, price lists), mapping profiles, validation with a dry run, reconciliation report, rollback of a migration batch, legacy-system extractors as plugins.

**Out:** Ongoing two-way legacy sync.

## Implementation notes

- Every migration is staged, validated and reconciled before commit; opening balances must balance before they can post.
- A migration batch is reversible as a unit until the shop goes live.
- The reconciliation report is the sign-off artefact: counts and values in the source vs the system.

## Acceptance criteria

1. Importing 10,000 items, 1,000 parties and opening balances produces a clean reconciliation that ties out.
2. Unbalanced opening balances are refused with a precise difference report.
3. A committed pre-go-live migration batch can be rolled back completely.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
