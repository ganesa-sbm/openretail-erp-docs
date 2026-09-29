# TASK-028 — Rule authoring, simulation and activation workflow

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.rules` |
| Traces to | FR-174..FR-178, BR-051, BR-070 |
| Depends on | TASK-027 |
| Size | L |
| Primary role | Rules engineer |

## Goal

Let a trained non-programmer change a discount policy and see the consequences before it goes live.

## Scope

**In:** Rule templates, guided editor, fact browser, draft versions, simulation against fixtures and the recorded transaction corpus, diff report, four-eyes approval, activation, one-click rollback.

**Out:** Free-form DRL editing for end users (developer-only).

## Implementation notes

- Simulation replays real recorded transactions and reports revenue/tax/discount deltas and worst-case per-invoice change — the number that catches mistakes.
- Activation requires a different user than the author; both are audited with the simulation attached.
- Rollback restores the previous package version atomically and is itself audited.

## Acceptance criteria

1. A template-authored discount produces a correct simulation diff on the corpus and activates within the documented propagation time.
2. Activation by the author alone is refused.
3. Rollback after activation restores prior pricing at the counter without a restart.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
