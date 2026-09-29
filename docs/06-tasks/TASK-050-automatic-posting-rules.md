# TASK-050 — Automatic posting rules from domain events

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.accounting` |
| Traces to | FR-415..FR-420, BR-045 |
| Depends on | TASK-049 |
| Size | L |
| Primary role | Accounting specialist |

## Goal

Turn operational events into correct accounting without anyone typing a journal.

## Scope

**In:** Posting-rule matrix (event → debit/credit accounts), event subscribers for sale, return, receipt, purchase invoice, payment, adjustment, production, session close; idempotent posting keyed by source document; posting failure quarantine.

**Out:** Manual voucher entry (TASK-049).

## Implementation notes

- Posting is idempotent by source document id — a redelivered event must not post twice (the outbox is at-least-once).
- A posting failure must never silently drop: quarantine the event, alert, and block period close until resolved.
- The rule matrix is data, reviewed by the accounting specialist, and covered by golden-file tests per scenario.

## Acceptance criteria

1. Every scenario in the posting-rule matrix produces the documented entry (golden-file tests).
2. Replaying the day's events produces no duplicate journal lines.
3. An unmapped account causes a quarantined, alerted failure rather than a wrong posting.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
