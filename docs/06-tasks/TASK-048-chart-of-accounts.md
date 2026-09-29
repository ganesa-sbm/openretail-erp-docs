# TASK-048 — Chart of accounts and accounting masters

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.accounting` |
| Traces to | FR-401..FR-406, BR-043 |
| Depends on | TASK-006 |
| Size | M |
| Primary role | Accounting specialist |

## Goal

Set up the books so every automatic posting has somewhere correct to go.

## Scope

**In:** Account groups and accounts, account types and normal balances, templates per vertical/region, control accounts, cost centres, financial-year and period masters, opening balance entry.

**Out:** Consolidation across legal entities.

## Implementation notes

- Control accounts (receivables, payables, inventory, GRNI, tax, round-off, loyalty) are mandatory and mapped at setup; the system refuses to operate with any unmapped.
- A transacted account can be deactivated but never deleted.
- Templates seed a working chart for a typical shop in minutes — setup friction is a real adoption blocker.

## Acceptance criteria

1. Applying a template creates a complete chart with all control accounts mapped and a balanced zero trial balance.
2. Opening balances import with a mandatory balance check before commit.
3. Deleting a transacted account is refused; deactivation is audited.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
