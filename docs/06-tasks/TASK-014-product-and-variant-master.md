# TASK-014 — Product and variant master

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory` |
| Traces to | FR-101..FR-108, BR-001..BR-004 |
| Depends on | TASK-006 |
| Size | L |
| Primary role | Core backend |

## Goal

Model what a shop sells, across verticals that describe items very differently.

## Scope

**In:** Item groups/categories, products, variant axes and variants, attributes, images, status, per-store activation, search index, bulk edit and CSV import/export.

**Out:** Vertical-specific attribute UIs (plugins, TASK-076..078).

## Implementation notes

- Variants are generated from axes (e.g. size × colour) with an explicit matrix, not free text — textiles depend on this.
- Custom attributes are typed and declared via `EntityAttributeSchema` so plugins extend items without schema changes.
- Search must return within the POS budget on 100k variants — maintain a search projection, do not LIKE-scan.
- Products are never hard-deleted once transacted; they are discontinued.

## Acceptance criteria

1. Creating a 10×12 size/colour matrix produces 120 variants with distinct codes in one operation.
2. Item search P95 under the documented budget at 100k variants on the minimum hardware profile.
3. CSV import of 10,000 items reports per-row errors without aborting the whole file.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
