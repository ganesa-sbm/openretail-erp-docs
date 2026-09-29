# ADR-007 — REST/JSON for client–server communication

Status: ACCEPTED · Date: 2026-09-29

## Context

The JavaFX client, Vaadin layer, mobile app, sync agents and third-party integrations all need a server contract. Candidates: REST/JSON, gRPC, GraphQL, Spring Remoting/Hessian.

## Decision

REST/JSON over HTTPS with an OpenAPI 3 contract. gRPC may be introduced later **only** for the sync channel if profiling shows JSON overhead is material, and only behind an ADR amendment.

## Rationale

- Debuggable with curl/browser at a shop with no specialist tooling — a decisive operational factor for field support.
- Native fit with Spring Boot + Spring Security; no extra proxy/HTTP2 requirements on old Windows machines.
- Third parties and implementers can integrate without generated stubs.
- Payload sizes here are small; the dominant costs are database and printing, not serialisation.

## Consequences

**Positive**: lowest friction for support and integrators; one contract for all clients; OpenAPI drives generated clients where wanted.

**Negative**: chattier and larger on the wire than protobuf; no streaming primitives (mitigated by chunked sync batches and cursor pagination); contract discipline must be enforced by tests (contract tests + OpenAPI diff check in CI) rather than by a compiler.
