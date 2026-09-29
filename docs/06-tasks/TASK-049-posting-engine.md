# TASK-049 — Voucher entry and the double-entry posting engine

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.accounting` |
| Traces to | FR-407..FR-414, BR-044, ADR-002 |
| Depends on | TASK-048 |
| Size | XL |
| Primary role | Accounting specialist |

## Goal

Build the ledger core: balanced, immutable, append-only, and impossible to bypass.

## Scope

**In:** Voucher types (journal, payment, receipt, contra, sale, purchase, credit/debit note), voucher and journal-line model, balance enforcement, posting service, reversal, narration and references, recurring vouchers.

**Out:** Automatic postings from domain events (TASK-050).

## Implementation notes

- No journal line is ever written outside the posting service; the table is otherwise write-protected (architecture and DB-role enforced).
- A voucher that does not balance to zero cannot be posted — asserted in the domain, not just the UI.
- Posted vouchers are immutable; the only correction is a reversal referencing the original.
- Posting into a locked period is refused (TASK-055).

## Acceptance criteria

1. Property test: for any generated voucher set, the sum of debits equals the sum of credits and the trial balance is zero.
2. A reversal exactly negates the original and both remain visible with their link.
3. Direct SQL insert into `journal_line` by the application role is denied.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
