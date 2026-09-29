# TASK-087 — Lanterna terminal client (Phase 4 resilience)

| Field | Value |
| --- | --- |
| Phase | 4 |
| Module(s) | `erp.client.tui` |
| Traces to | BR-075, FR-629..FR-632, NFR-022 |
| Depends on | TASK-070 |
| Size | L |
| Primary role | UI engineer |

## Goal

Keep a shop selling on hardware or in conditions where the graphical client cannot run.

## Scope

**In:** Terminal UI shell, minimal billing flow (scan/enter item, quantity, tender, print), stock lookup, day close, login, local and thin modes, ESC/POS printing from the terminal, SSH-friendly operation.

**Out:** Full back-office functionality in the terminal.

## Implementation notes

- Scope is deliberately minimal: sell, look up, close. Anything more dilutes the resilience purpose.
- It reuses the same application services — no reimplementation of billing logic.
- It must run on a machine with 1 GB RAM and no desktop environment, over SSH if needed.

## Acceptance criteria

1. A complete sale with printing is possible in the terminal client on a headless machine.
2. The same invoice invariants and postings result as from the graphical client (shared test scenarios).
3. Startup and memory use fit the minimum profile documented in NFR-022.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
