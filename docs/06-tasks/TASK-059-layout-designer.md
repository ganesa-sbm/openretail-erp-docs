# TASK-059 — Layout designer (admin)

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.client.fx` |
| Traces to | FR-512..FR-516 |
| Depends on | TASK-058 |
| Size | L |
| Primary role | UI engineer |

## Goal

Let an implementation consultant tailor screens without editing JSON.

## Scope

**In:** Drag-and-drop canvas, component palette, property panel, binding picker from the data context, live preview with sample data, validation panel, version history, publish and rollback, three-way diff on core upgrades.

**Out:** End-user (shopkeeper) editing — permission-gated to consultants/admins.

## Implementation notes

- The designer edits the same payload the runtime consumes — no separate design format that can drift.
- Upgrade safety: an override records the core version it derives from and offers a three-way merge when core changes.
- Every publish is a new version with an audit entry and instant rollback.

## Acceptance criteria

1. A consultant adds a field, reorders the form, publishes and sees it at a counter within the documented propagation time.
2. An invalid design cannot be published; errors point at the offending component.
3. A core upgrade to an overridden screen presents a merge choice rather than silently overwriting or silently ignoring.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
