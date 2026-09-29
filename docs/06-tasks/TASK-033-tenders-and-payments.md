# TASK-033 — Tenders, split payments and payment terminals

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.sales.pos` |
| Traces to | FR-227..FR-232, BR-027 |
| Depends on | TASK-031 |
| Size | M |
| Primary role | Core backend |

## Goal

Accept how customers actually pay: cash, card, UPI/wallet, credit, gift card, and combinations.

## Scope

**In:** Tender type master with ledger mapping, split tender, change calculation, rounding, tender-level charges, `PaymentProcessor` SPI use, reversal on failed capture, tips/charges where applicable.

**Out:** Specific gateway implementations (plugins, TASK-078+).

## Implementation notes

- Tender types are configuration mapped to ledger accounts — adding one must not require code.
- Card/UPI capture is asynchronous and idempotent; a timeout must never double-charge or silently lose a payment (reconciliation state: pending, confirmed, failed).
- Credit tender checks the customer's limit and posts to receivables, not cash.

## Acceptance criteria

1. A split of cash + card + credit balances exactly and posts to three distinct accounts.
2. A terminal timeout leaves the bill in a recoverable pending state with an explicit operator resolution step, audited.
3. Change and round-off are correct across the rounding configurations in the test matrix.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
