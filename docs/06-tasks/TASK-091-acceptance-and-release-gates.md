# TASK-091 — Acceptance test suite and release gates

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `testing` |
| Traces to | TS-05, BR-079 |
| Depends on | TASK-082 |
| Size | L |
| Primary role | QA |

## Goal

Define, once, what 'ready to put in a shop' means, and automate the check.

## Scope

**In:** End-to-end acceptance scenarios per vertical, a day-in-the-life scenario across counters, chaos scenarios (power loss, printer failure, server failure, disk full, clock skew, corrupt plugin, restore from an old backup), release gate checklist and CI wiring, exploratory test charters.

**Out:** Certification.

## Implementation notes

- A release gate that can be waived informally is not a gate: waivers require a named approver and are recorded in the release notes.
- Chaos scenarios run against real installers on clean VMs, not against a dev environment.
- The day-in-the-life scenario is the headline test: open, sell all day across counters offline and online, close, reconcile, report.

## Acceptance criteria

1. All acceptance scenarios pass on a clean-VM install of the release candidate.
2. Every chaos scenario ends with data integrity intact and a documented recovery path executed.
3. The release gate checklist is enforced in the pipeline and blocks tagging when unmet.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
