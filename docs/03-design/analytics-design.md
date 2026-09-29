# Analytics and dashboards design

Implements BR-062, BR-063.

## 1. Approach

No separate warehouse in Phase 2 — analytics runs on **projections** in the operational database (data architecture §5), computed incrementally from domain events and rebuilt nightly. In cloud mode (Phase 5), the cloud tenant holds consolidated projections across stores, which is where multi-store and forecasting workloads live so store hardware is unaffected.

## 2. Projections

| Projection | Grain | Fields |
| --- | --- | --- |
| `sales_daily_agg` | store × date × category × channel | bills, units, gross, discount, tax, net, cogs, margin |
| `sales_item_agg` | store × date × variant | units, net, margin, returns |
| `cashier_daily_agg` | store × date × user | bills, net, discount given, voids, returns, drawer variance |
| `stock_snapshot` | store × variant × batch × location × date | qty, value, age days, days to expiry |
| `purchase_agg` | store × date × supplier | receipts, value, returns |
| `customer_agg` | customer × month | visits, spend, margin, last visit, points |
| `tender_agg` | store × date × tender type | count, amount |

Incremental update on commit (same transaction for correctness-critical ones, outbox for the rest) + nightly full rebuild that also verifies the incremental result (NFR-023).

## 3. Dashboards

| Dashboard | Audience | Cards |
| --- | --- | --- |
| Today | Owner, Manager | Net sales vs yesterday/last week, bills, average bill, cash in drawer, top 10 items, tender mix, hourly sales curve, live alerts |
| Sales | Owner | Trend (day/week/month), margin %, category mix, discount impact, returns rate, same-store comparison |
| Inventory | Manager | Stock value, ageing buckets, near-expiry value, dead stock, reorder list, negative-stock exceptions, shrinkage trend |
| Purchasing | Owner | Spend by supplier, price variance, receipt accuracy, payables ageing |
| Cash & books | Accountant | Cash/bank position, receivables/payables ageing, tax liability, unposted items, period status |
| Staff | Owner | Sales per cashier, discounts given, voids, variance, hours |
| Health | Support/Owner | Node status, last sync, pending changes, conflicts, backup age, version drift, printer errors |

Every card drills through to the underlying report (report designer) and then to documents, so a number is always traceable to its transactions.

## 4. Query and refresh behaviour

Dashboard cards are named queries with an explicit permission and data scope; results are cached with a card-specific TTL (Today: 60 s; monthly trends: 15 min) and refreshed on relevant events. On a counter node in local mode, dashboards show local-store data with a "local data only" marker rather than failing.

## 5. Forecasting (Phase 5)

Deliberately simple, explainable and offline-computable:

- **Baseline**: moving average and exponentially weighted moving average of daily demand per variant.
- **Seasonality**: day-of-week factors and month factors derived from ≥ 12 months of history; festival calendar as an exogenous flag list maintained per tenant.
- **Reorder point**: `ROP = demand_during_lead_time + safety_stock`, safety stock from demand variability and a configurable service level; suggested order quantity respects supplier pack size and MOQ.
- **Output**: a reorder suggestion list with the reasoning shown (average demand, lead time, variability, resulting ROP), feeding `REORDER_SUGGESTION` decisions and purchase-order drafts.
- No opaque ML in v1: buyers must be able to see and override the arithmetic; accuracy is tracked (MAPE per category) and shown so trust is earned before automation is increased.

## 6. Exports

Every dashboard card and report exports to CSV/XLSX; scheduled exports (daily/weekly) can be emailed via the notification service. A documented read-only analytics schema/view set lets owners connect external BI tools (Metabase, Superset) to the store server or cloud without touching operational tables.

## 7. Performance

Dashboards must not degrade POS latency: analytics queries run with a lower statement timeout, hit projections only, and on the store server are routed to a read replica when configured. Budgets: Today dashboard ≤ 1.5 s cold, ≤ 300 ms cached; any card exceeding 3 s in CI against the reference dataset fails the build.
