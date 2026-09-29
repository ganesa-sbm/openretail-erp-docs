# TASK-090 — User documentation, in-app help and training materials

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `docs` |
| Traces to | BR-078, NFR-012 |
| Depends on | TASK-032 |
| Size | M |
| Primary role | Technical writer |

## Goal

Reduce support load and make the software learnable by the people who will actually use it.

## Scope

**In:** Role-based user guides (cashier, storekeeper, manager, accountant, owner), in-app contextual help, keyboard cheat sheet, short task videos, admin/installation guide, troubleshooting guide, release notes discipline.

**Out:** Developer documentation (already in this docs set).

## Implementation notes

- Documentation is task-oriented ('take a return', 'close the day'), not feature-oriented — that is how shop staff look for help.
- In-app help is context-linked from each screen and works offline (bundled, not a web link).
- Screenshots are generated from the test suite so they cannot go stale silently.

## Acceptance criteria

1. Every role guide covers its documented daily tasks end to end and is validated by a new user completing them unaided.
2. In-app help opens the right topic from every core screen, offline.
3. Release notes are produced for each release with user-visible changes and upgrade notes.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
