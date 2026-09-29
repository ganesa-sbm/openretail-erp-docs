# TASK-009 — Authentication: login, password policy, offline cached credentials, JWT

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.security` |
| Traces to | BR-060, NFR-009, FR-505 |
| Depends on | TASK-008 |
| Size | L |
| Primary role | Core backend |

## Goal

Let staff log in reliably — including on a counter with no server — without weakening credentials.

## Scope

**In:** Argon2id hashing (BCrypt verification for imported hashes), password policy, lockout, session/JWT issuance and refresh, offline credential cache with TTL, PIN/quick-switch for counters, forced password change.

**Out:** SSO; 2FA (backlog).

## Implementation notes

- Offline cache stores only the verifier (Argon2id hash) plus grant snapshot, encrypted at rest, with a configurable max offline age; expiry blocks login rather than degrading silently.
- JWT is short-lived with refresh; tokens carry role/scope claims and a node id.
- Lockout is per user with exponential delay; cashier quick-switch PIN is a separate weaker factor usable only for same-shift switching and never for admin actions.
- All auth outcomes are audited, including failures with source node.

## Acceptance criteria

1. A counter disconnected from the server for the configured window still authenticates known users; beyond the window it refuses and says why.
2. Argon2id parameters meet the documented cost and are benchmarked on the minimum hardware profile (<300 ms).
3. Brute-force attempt is throttled and audited; tokens cannot be replayed after logout.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
