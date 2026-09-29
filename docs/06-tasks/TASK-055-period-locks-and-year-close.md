# TASK-055 — Period locks, year-end close and opening carry-forward

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.accounting` |
| Traces to | FR-444..FR-449, BR-048, CMP-06 |
| Depends on | TASK-053 |
| Size | M |
| Primary role | Accounting specialist |

## Goal

Stop the past from changing, and roll into the new year cleanly.

## Scope

**In:** Period lock per module with permissioned unlock, soft close and hard close, year-end closing entries, P&L transfer to retained earnings, opening balance carry-forward, year snapshot with hash, post-close adjustment handling.

**Out:** Multi-entity consolidation.

## Implementation notes

- A locked period refuses all postings including automatic ones — the quarantine path (TASK-050) must handle this and alert rather than drop.
- Unlock is heavily permissioned, time-boxed and audited; every posting made during an unlock is flagged.
- The year snapshot hash makes later tampering detectable (CMP-06).

## Acceptance criteria

1. Posting into a locked period fails everywhere, including event-driven postings, with a clear quarantined alert.
2. Year close produces balanced opening balances; the new year's opening trial balance is zero-sum and matches the prior close.
3. Snapshot hash of a closed year changes if any historical row is altered.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
