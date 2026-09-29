# TASK-064 — Notification subscriptions and alert catalogue

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.notification` |
| Traces to | FR-540..FR-543 |
| Depends on | TASK-063 |
| Size | M |
| Primary role | Core backend |

## Goal

Decide who gets told what, per shop, without code changes.

## Scope

**In:** Alert catalogue (low stock, near expiry, large discount, day close, backup failed, sync lag, credit limit breach, payment due), subscription model per role/user/channel, thresholds, digest vs immediate, test-send.

**Out:** Analytics-driven anomaly alerts (TASK-066).

## Implementation notes

- Every alert declares its severity, default channel and default subscribers; shops adjust rather than start from nothing.
- Digests exist because immediate alerts for frequent events get muted, and a muted alert is worse than none.
- Operational alerts (backup failed, sync stalled) must reach the owner even if business alerts are muted.

## Acceptance criteria

1. Each catalogue alert can be triggered in a test harness and delivers to the configured subscribers.
2. Digest mode batches correctly over the configured window.
3. Backup-failure alert is delivered even with all business alerts disabled.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
