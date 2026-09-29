# TASK-067 — Change log, sync protocol and server endpoints

| Field | Value |
| --- | --- |
| Phase | 3 |
| Module(s) | `erp.sync, erp.server` |
| Traces to | FR-555..FR-562, ADR-001, BR-057 |
| Depends on | TASK-006 |
| Size | XL |
| Primary role | Architect |

## Goal

Build the mechanism that makes multi-node offline-first operation actually converge.

## Scope

**In:** `change_log` capture, sync tokens/watermarks, push/pull/ack protocol, chunking and compression, idempotent application, per-entity conflict policies, server endpoints, backpressure, resumable transfer.

**Out:** UI for conflicts (TASK-075); cloud tier (TASK-089).

## Implementation notes

- Append-only facts merge without conflict; only mutable master data can conflict, and each entity declares its policy (sync-architecture §3).
- Every change carries an id making application idempotent — the protocol assumes duplicate delivery.
- Transfers are resumable and chunked: a 2G link dropping mid-sync must not restart from zero.
- A node may be offline for months; the protocol must handle wide watermark gaps and schema drift across releases.

## Acceptance criteria

1. The simulator converges all nodes under partitions, duplicate delivery, reordering and crash-restart, for 1,000 randomized seeds.
2. Sync of a day's transactions for 5 counters completes within the documented budget on a slow link.
3. Interrupting sync at any point leaves both sides consistent and resumes correctly.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
