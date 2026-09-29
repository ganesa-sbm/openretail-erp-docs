# TASK-037 — Offline document numbering and series management

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.sales.pos, erp.core.kernel` |
| Traces to | FR-251..FR-254, ADR-006, BR-032, CMP-01 |
| Depends on | TASK-011 |
| Size | M |
| Primary role | Core backend |

## Goal

Guarantee unique, gapless-per-series, statutorily acceptable document numbers on nodes that cannot talk to each other.

## Scope

**In:** Series master per document type/store/counter/financial year, counter-prefixed allocation, crash-safe local allocation, gap detection and reporting, year rollover, manual series for legacy continuity.

**Out:** Statutory e-invoice numbering (plugin).

## Implementation notes

- Numbers are allocated locally from a counter-prefixed series — no server round-trip on the billing path (ADR-006).
- Allocation is crash-safe: a number consumed but not committed is reported as a gap with its reason, never silently reused.
- Year rollover is automatic at the configured financial-year boundary and pre-provisioned so midnight billing does not fail.
- Gaps are visible to the accountant; unexplained gaps are an exception-report item.

## Acceptance criteria

1. Ten counters billing concurrently offline for a day produce zero duplicate numbers after sync (simulation test).
2. Killing the process between allocation and commit produces a reported gap, not a reused number.
3. Financial-year rollover at midnight during active billing does not fail a bill.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
