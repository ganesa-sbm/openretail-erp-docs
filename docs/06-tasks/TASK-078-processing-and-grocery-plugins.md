# TASK-078 — Processing/manufacturing and departmental plugins

| Field | Value |
| --- | --- |
| Phase | 3 |
| Module(s) | `erp.plugin.processing, erp.plugin.grocery` |
| Traces to | BR-068, FR-615..FR-620 |
| Depends on | TASK-023 |
| Size | L |
| Primary role | Core backend |

## Goal

Cover the remaining target verticals: light processing (grading, packing, yields) and departmental/grocery specifics.

## Scope

**In:** Processing: job orders, multi-output yields, grading, wastage norms and variance. Grocery: weighed items and scale integration, MRP-based pricing, shelf labels, quick combos/hampers, expiry-driven markdowns.

**Out:** Full MES; perishable procurement optimisation.

## Implementation notes

- Yield norms are configuration; variance beyond norm is an exception-report item, not silent shrinkage.
- Markdown on near-expiry stock runs through `NEAR_EXPIRY_ACTION` so the policy is shop-configurable.
- Scale-weighed items reuse the core peripheral SPI.

## Acceptance criteria

1. A processing job with three outputs and wastage posts correct movements and costs, with variance reported.
2. A near-expiry markdown applies automatically per policy and is visible in the price trail.
3. Weighed items bill correctly from the scale within the POS latency budget.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
