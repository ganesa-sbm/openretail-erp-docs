# TASK-092 — Pilot deployment and hypercare

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `delivery` |
| Traces to | BR-080 |
| Depends on | TASK-091 |
| Size | M |
| Primary role | Implementation consultant |

## Goal

Validate the product where it matters — in a real shop, with real money — before wider rollout.

## Scope

**In:** Pilot site selection across two verticals, data migration and configuration, staff training, go-live checklist, daily hypercare review for two weeks, defect triage loop, success metrics capture, rollout playbook produced from what was learned.

**Out:** Wide commercial rollout.

## Implementation notes

- Pilot in at least two different verticals (e.g. a departmental store and a paint/hardware shop) — single-vertical pilots hide assumptions.
- Run the legacy system in parallel for the first week where feasible, and reconcile daily.
- Every hypercare defect gets a regression test before it is closed; that is how the pilot improves the product rather than just the site.

## Acceptance criteria

1. Two pilot shops operate for four weeks with daily reconciliation matching the legacy system or manual records.
2. Success metrics (bills/hour, stock variance at first stock-take, support tickets/week, backup success rate) are captured and meet the documented targets.
3. The rollout playbook and an updated go-live checklist are published from the pilot experience.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
