# TASK-083 — Security hardening, dependency scanning and secure defaults

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.security, build` |
| Traces to | NFR-009..NFR-011, BR-069 |
| Depends on | TASK-010 |
| Size | M |
| Primary role | Architect |

## Goal

Reduce the attack surface of software sitting on a shop LAN with no IT staff.

## Scope

**In:** Secure defaults review, field-level encryption implementation and key management/rotation, TLS configuration and certificate lifecycle, secrets handling, dependency vulnerability scanning (OWASP/OSV) in CI, SAST, a documented threat-model review gate, penetration-test checklist.

**Out:** Formal certification.

## Implementation notes

- Defaults must be safe without configuration: TLS on, strong hashing, no default passwords, least-privilege DB roles.
- Encryption keys are per-tenant, wrapped by a node key, and rotatable without downtime or re-encrypting everything synchronously.
- A new or updated dependency with a known critical vulnerability fails the build; exceptions are time-boxed and recorded.

## Acceptance criteria

1. A fresh install passes the hardening checklist with no manual steps.
2. Key rotation completes online and old data remains readable (tested).
3. The CI scan fails on a deliberately introduced vulnerable dependency.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
