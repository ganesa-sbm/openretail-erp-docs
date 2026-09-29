# TASK-027 — Drools integration and the decision service

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.rules` |
| Traces to | FR-169..FR-173, ADR-004, BR-050 |
| Depends on | TASK-004 |
| Size | L |
| Primary role | Rules engineer |

## Goal

Make selected business policies changeable without a release, safely.

## Scope

**In:** `DecisionService`, the eight decision points, immutable fact models, KieContainer lifecycle and hot reload, timeouts and fallbacks, per-decision metrics.

**Out:** Rule authoring UI (TASK-028).

## Implementation notes

- Rules are pure functions of facts: no service calls, no I/O, no mutation of inputs (enforced by a fact-model API and review).
- Every decision point has a documented default outcome used when no rule fires or evaluation exceeds its timeout — the counter must never hang on rules.
- Rule packages are versioned artefacts; hot reload swaps containers atomically without dropping in-flight decisions.
- Every decision emits a trail (rules fired, inputs hash, outcome) for the price/approval trail.

## Acceptance criteria

1. Decision evaluation P95 within the documented budget at the POS with the reference rule set.
2. A rule package that loops or times out falls back to the default and raises an alert rather than blocking billing.
3. Hot reload under load produces no failed or mixed-version decisions (concurrency test).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
