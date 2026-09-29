# TASK-054 — Bank accounts, cash book and reconciliation

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.accounting` |
| Traces to | FR-438..FR-443, BR-049 |
| Depends on | TASK-049 |
| Size | M |
| Primary role | Accounting specialist |

## Goal

Tie the books to the bank, which is where errors and fraud surface.

## Scope

**In:** Bank account master, cash/bank book, deposits and withdrawals, cheque management, statement import (CSV/MT940-style), auto-matching with rules, manual matching, reconciliation statement with unreconciled items.

**Out:** Direct bank API integration (Phase 5).

## Implementation notes

- Matching is suggestion-based with explicit confirmation; auto-match must be reversible.
- Reconciliation produces a statement showing book balance, bank balance and every reconciling item.
- Unreconciled items older than a threshold appear in the exception report.

## Acceptance criteria

1. Importing a statement matches the documented percentage of the fixture's transactions automatically and lists the rest.
2. Reconciliation statement balances: book + reconciling items = bank balance.
3. Un-matching a wrongly matched pair restores both to unreconciled, audited.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
