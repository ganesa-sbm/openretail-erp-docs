# Practice card — Add a new module

**When**: a new bounded context (not a variant feature — that is a plugin) must be added.
**Prerequisites**: SKL-002/2, SKL-003/3. Read `system-architecture.md` §5.

## Steps

1. **Justify it.** A new module needs an ADR if it introduces a new dependency direction or a new persistence schema. A feature inside an existing context does not.
2. **Create the Gradle subproject** `erp-<name>` with `module-info.java`:
   ```java
   module erp.<name> {
       requires erp.core.domain;
       exports com.openretail.<name>.api;      // interfaces, DTOs, events only
       // internals are NOT exported
   }
   ```
3. **Package layout**: `api/` (published types), `domain/` (aggregates, VOs, domain services), `application/` (use cases, transactions), `infrastructure/` (JPA, adapters).
4. **Domain first**: write aggregates and invariant tests before persistence. The domain package must not import Spring, Hibernate or JavaFX (ArchUnit enforces this).
5. **Persistence**: entities in `infrastructure`, mapped to the domain via explicit mappers — no JPA annotations on aggregates. Add Flyway migrations (see `how-to-write-a-migration.md`).
6. **Application services**: one public method per use case, `@Transactional`, `@RequiresPermission`, returning a result record. Emit domain events through the outbox; write the audit event in the same transaction.
7. **Register the data context** if the module has screens, so layouts can bind to it (`TS-03` §5).
8. **API**: add endpoints per `TS-01` conventions; regenerate and commit the OpenAPI diff.
9. **Sync**: register persisted tables with the sync module, declaring their class (append-only fact vs mutable master) and conflict policy (`sync-architecture.md` §3). A table that is not registered will silently not replicate — the checklist below catches this.
10. **Tests**: unit (invariants), integration (Testcontainers, both dialects), contract (API), sync simulation scenario if the module owns replicated data.
11. **Docs**: add or update the functional spec in `docs/04-specifications/` and link it from the traceability matrix.

## Checklist before review

- [ ] No cycles (`./gradlew checkArchitecture` passes)
- [ ] Domain has zero framework imports
- [ ] Every use case: permission, transaction, audit event, domain event
- [ ] Migrations forward-only and tested from N-3
- [ ] Tables registered with sync + conflict policy documented
- [ ] Spec and traceability updated
- [ ] Performance: no N+1 (assert query counts in integration tests)
