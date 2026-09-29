# TASK-088 — Compose Multiplatform mobile app (Phase 3)

| Field | Value |
| --- | --- |
| Phase | 3 |
| Module(s) | `mobile` |
| Traces to | BR-076, FR-633..FR-638 |
| Depends on | TASK-069 |
| Size | XL |
| Primary role | Mobile UI engineer |

## Goal

Put the tasks that happen away from a counter on a phone or tablet: counting, receiving, checking, approving.

## Scope

**In:** Compose Multiplatform app (Android first, iOS-ready), auth against the store server, stock lookup and price check, stock-take counting with the camera/scanner, goods receipt, approvals, owner dashboards, offline queue with sync.

**Out:** Full POS billing on mobile.

## Implementation notes

- The mobile app is an API client with its own local queue — it must work in a basement stockroom with no signal and sync later.
- It uses the same REST API and the same permissions; no mobile-specific backend.
- Camera scanning must be fast enough for counting hundreds of items; support external Bluetooth scanners.

## Acceptance criteria

1. Offline stock-take of 500 items syncs correctly on reconnect with no duplicates.
2. Approvals performed on mobile are audited identically to desktop approvals.
3. The app runs on the documented minimum Android version and device profile.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
