# TASK-036 — Customers, credit accounts and loyalty

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.sales.pos` |
| Traces to | FR-244..FR-250, BR-030, BR-031 |
| Depends on | TASK-031 |
| Size | L |
| Primary role | Core backend |

## Goal

Support the credit ledger and repeat-customer behaviour these shops run on.

## Scope

**In:** Customer master with PII encryption, credit limit and ageing, on-account sales and receipts, statements, loyalty points accrual/redemption via rules, customer groups, quick-create at the counter.

**Out:** Marketing campaigns (backlog).

## Implementation notes

- Customer PII (phone, address, identifiers) is encrypted at rest with the documented key strategy (NFR-010).
- Credit limit enforcement is a hard block with an approval override; ageing buckets are configurable.
- Loyalty accrual/redemption runs through `LOYALTY_ACCRUAL`; points are a liability and must post to the ledger, not float outside it.
- Quick-create at the counter must take seconds — phone number is the minimum viable identity.

## Acceptance criteria

1. An over-limit sale is blocked and only proceeds with an audited approval.
2. Customer statement ties exactly to the receivables control account for the fixture.
3. Points earned and redeemed reconcile to the loyalty liability account balance.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
