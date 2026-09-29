# TASK-082 — Performance test suite and budgets in CI

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `testing` |
| Traces to | TS-05, NFR-001..NFR-005 |
| Depends on | TASK-032 |
| Size | M |
| Primary role | QA |

## Goal

Stop performance regressions at the PR, not at the counter.

## Scope

**In:** Fixture datasets (Small/Medium/Large), JMH micro-benchmarks for hot paths, k6/Gatling API load tests, TestFX-based UI latency measurement, budget assertions, trend reporting, minimum-hardware-profile runner.

**Out:** Production profiling (TASK-012).

## Implementation notes

- Budgets come from the NFRs and fail the build when breached — a budget nobody enforces is a wish.
- Measurements run on a pinned runner profile approximating the minimum hardware, otherwise the numbers are meaningless.
- The Large fixture models a real busy shop: 100k variants, 2 years of transactions, 10 counters.

## Acceptance criteria

1. POS scan, finalise, price resolution, dashboard load and trial balance all have enforced budgets in CI.
2. A deliberate 2× slowdown on a hot path fails the pipeline.
3. Trend charts show per-commit performance history.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
