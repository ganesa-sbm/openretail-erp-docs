# TASK-069 — Store server application and REST API v1

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.server` |
| Traces to | TS-01, FR-569..FR-574, ADR-007 |
| Depends on | TASK-010 |
| Size | L |
| Primary role | Core backend |

## Goal

Provide the LAN tier: a Spring Boot server exposing the API that thin clients, web clients and sync use.

## Scope

**In:** Spring Boot application assembly, REST controllers per module following TS-01, JWT auth filter, idempotency keys, optimistic concurrency via ETag/If-Match, RFC 7807 errors, pagination, rate limits, OpenAPI generation, TLS configuration.

**Out:** Public internet exposure (Phase 5).

## Implementation notes

- Idempotency keys are mandatory on all POST endpoints that create documents — retries over flaky LAN must not duplicate invoices.
- The API is generated into OpenAPI and diffed in CI; a breaking change fails the build unless the version is bumped.
- TLS by default with a store CA; HTTP is only permitted on an explicit developer flag.

## Acceptance criteria

1. Contract tests cover every endpoint for auth, permissions, scope, validation and error shape.
2. Replaying a POST with the same idempotency key returns the original result and creates nothing new.
3. OpenAPI diff gate blocks an unversioned breaking change (proved with a deliberate change).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
