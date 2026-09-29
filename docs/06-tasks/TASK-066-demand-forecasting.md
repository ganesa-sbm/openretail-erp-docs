# TASK-066 — Demand forecasting and explainable suggestions

| Field | Value |
| --- | --- |
| Phase | 5 |
| Module(s) | `erp.analytics` |
| Traces to | FR-551..FR-554, BR-056 |
| Depends on | TASK-065 |
| Size | M |
| Primary role | Core backend |

## Goal

Help shops order the right quantity, with methods they can understand and override.

## Scope

**In:** Moving average and seasonal-naive baselines, trend and seasonality detection, safety-stock computation from lead time and variability, forecast accuracy tracking, explanation of every suggestion, manual override with reason.

**Out:** Machine-learning models (out of scope until accuracy tracking justifies them).

## Implementation notes

- Start with simple, explainable statistics; a shopkeeper will ignore a number they cannot interrogate.
- Track forecast error (MAPE/bias) per item so the system can say when it is unreliable and fall back.
- Forecasts feed the reorder decision point as an input, never as an automatic order.

## Acceptance criteria

1. Forecast accuracy is reported per item/category and improves on a naive baseline for the seasonal fixture.
2. Each suggestion shows the history, lead time, safety stock and resulting quantity.
3. Forecasting runs within its scheduled window without affecting POS latency (measured under concurrent load).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
