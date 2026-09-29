# Document and engineering conventions

## Documents

- Markdown, one topic per file, ATX headings, tables over prose lists where comparable.
- Every requirement gets a stable identifier; identifiers are never reused after deletion (mark `WITHDRAWN`).
- Diagrams are Mermaid fenced blocks so they render in Git hosting and stay diffable.
- Status field on each document: `DRAFT` → `REVIEW` → `APPROVED` → `SUPERSEDED`.

## Requirement wording

- `MUST` — mandatory for the phase in which it appears.
- `SHOULD` — expected, may be deferred with a recorded decision.
- `MAY` — optional/plugin territory.

## Code conventions referenced by the specs

- Java 21, JPMS modules, one Gradle subproject per module (`erp-core-domain`, `erp-inventory`, …).
- Package root `com.openretail.<module>`; public API lives in `…​.api`, internals in `…​.internal` (not exported).
- Money is `BigDecimal` with scale 4 internally, rounded to currency scale at presentation; never `double`.
- Quantities are `BigDecimal` scale 3.
- All timestamps stored UTC as `timestamptz`; display in store timezone.
- Every mutable business table carries `id UUID`, `node_id`, `version`, `created_at`, `updated_at`, `updated_by`, `deleted` (soft delete) to support sync.
- IDs are UUIDv7 generated client-side so offline nodes can create records without coordination.

## Definition of done (per task document)

1. Code + unit tests merged, coverage not decreased.
2. Integration test (Testcontainers) for any persistence or API change.
3. Spec document updated if behaviour differs from spec.
4. Audit events emitted for any state-changing business operation.
5. Offline behaviour considered: does the operation work with no server? If yes, sync conflict rules stated.
