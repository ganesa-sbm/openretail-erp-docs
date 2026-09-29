# TASK-081 — Deterministic sync simulation harness

| Field | Value |
| --- | --- |
| Phase | 3 |
| Module(s) | `testing` |
| Traces to | TS-05, NFR-006 |
| Depends on | TASK-067 |
| Size | L |
| Primary role | QA |

## Goal

Make offline correctness testable instead of hoped for.

## Scope

**In:** In-memory multi-node simulator, scripted and randomized workloads, fault injection (partition, delay, reorder, duplicate, crash, clock skew, disk full), convergence and invariant assertions, seed replay and shrinking, CI integration with a nightly long run.

**Out:** Physical hardware test lab.

## Implementation notes

- The simulator drives the real sync code, not a model of it — otherwise it proves nothing.
- Every failing seed must be replayable and reducible to a minimal case; store failing seeds as regression tests.
- Invariants checked after convergence: identical hashes per entity, no duplicate document numbers, balanced ledgers, on-hand equals movement sums.

## Acceptance criteria

1. 1,000 randomized seeds converge with all invariants holding, nightly in CI.
2. A deliberately introduced conflict-policy bug is caught by the harness.
3. A failing seed is replayable deterministically and shrinks to a minimal scenario.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
