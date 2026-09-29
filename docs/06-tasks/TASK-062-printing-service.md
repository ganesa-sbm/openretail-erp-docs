# TASK-062 — Printing service, spooling and printer profiles

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.reporting` |
| Traces to | FR-529..FR-532, BR-033 |
| Depends on | TASK-060 |
| Size | M |
| Primary role | Core backend |

## Goal

Make printing reliable on hardware that is frequently offline, jammed or shared.

## Scope

**In:** Printer profile master (role → device), spool with persistent queue, retry and backoff, job status UI, reprint history, network and USB printers, per-node overrides.

**Out:** Driver-level device work (TASK-039).

## Implementation notes

- Templates bind to printer roles (receipt, A4, label), not devices, so counters can differ without template forks.
- The queue survives restarts; jobs are idempotent so recovery never double-prints.
- Job failures are visible and actionable in the UI, not buried in logs.

## Acceptance criteria

1. Printer offline then restored prints each queued job exactly once.
2. Restarting the client preserves the queue and job states.
3. Switching a counter's printer profile requires no template change.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
