# TS-01 — REST API specification

Base: `https://<store-server>:8443/api/v1` · Auth: `Authorization: Bearer <jwt>` · Media: `application/json`

## 1. Conventions

- IDs are UUIDv7 strings; money is a string decimal with explicit currency (`{"amount":"1180.0000","currency":"INR"}`); quantities are string decimals with a UOM id.
- Timestamps are RFC 3339 UTC; `businessDate` is a plain `YYYY-MM-DD`.
- Creating endpoints require `Idempotency-Key` (UUID); a repeat with the same key returns the original result with `Idempotent-Replay: true`.
- Optimistic concurrency on mutable resources via `If-Match: <version>`; mismatch → 409.
- Pagination: `?cursor=<opaque>&limit=<=200`; response carries `nextCursor`.
- Errors: RFC 7807 problem+json.

```json
{ "type": "https://openretail/errors/INSUFFICIENT_STOCK",
  "title": "Insufficient stock",
  "status": 422,
  "code": "INSUFFICIENT_STOCK",
  "detail": "Available 3, requested 5 for SKU TSH-BLK-M",
  "instance": "/api/v1/sales/invoices",
  "context": { "variantId": "0192…", "available": "3.000" } }
```

## 2. Authentication

| Endpoint | Purpose |
| --- | --- |
| `POST /auth/token` | `{username,password,deviceId}` → access (15 min) + refresh (12 h) |
| `POST /auth/pin` | `{userId,pin,deviceId}` → tokens; only for enrolled devices |
| `POST /auth/refresh` | Rotating refresh |
| `POST /auth/logout` | Revokes refresh token |
| `GET /auth/me` | User, roles, permissions, scopes |

Tokens carry `tenant`, `sub`, `roles`, `scopes`, `deviceId`, `jti`. Revocation list is synced to nodes.

## 3. Catalogue

```
GET    /catalogue/products?query=&categoryId=&cursor=
POST   /catalogue/products
GET    /catalogue/products/{id}
PATCH  /catalogue/products/{id}           (If-Match)
GET    /catalogue/variants/lookup?barcode=|code=|q=      ← POS hot path
POST   /catalogue/products/{id}/variants:generateMatrix
GET    /catalogue/price?variantId=&customerId=&qty=&date= ← price decision incl. reason trail
```

## 4. Inventory

```
GET  /inventory/stock?variantId=&locationId=&asOf=
GET  /inventory/movements?variantId=&from=&to=&cursor=
POST /inventory/adjustments            {lines[], reason, approvalToken?}
POST /inventory/transfers              {fromLocation,toLocation,lines[]}
POST /inventory/stock-takes            {scope, mode}
POST /inventory/stock-takes/{id}/lines {counts[]}
POST /inventory/stock-takes/{id}:approve
POST /inventory/production-orders      {bomId, plannedQty, components[], output}
```

## 5. Sales / POS

```
POST /sales/drawer-sessions            {counterId, openingFloat}
POST /sales/drawer-sessions/{id}:close {denominations[], countedTotal}
POST /sales/invoices                   ← finalise (Idempotency-Key required)
GET  /sales/invoices/{id}
POST /sales/invoices/{id}:void         {reason, approvalToken}
POST /sales/returns                    {originalInvoiceId?, lines[], refundTenders[]}
GET  /sales/invoices?number=&customerPhone=&from=&to=
POST /sales/parked-bills               (local-only in counter mode; server endpoint for thin clients)
```

`POST /sales/invoices` request (abridged):

```json
{ "counterId": "0192…", "businessDate": "2026-09-29", "customerId": null,
  "lines": [ { "variantId": "0192…", "batchId": "0192…", "qty": "2.000", "uomId": "0192…",
               "unitPrice": {"amount":"499.0000","currency":"INR"},
               "discount": {"type":"PERCENT","value":"5"},
               "tax": [ {"component":"CGST","rate":"9","amount":"42.6600"},
                        {"component":"SGST","rate":"9","amount":"42.6600"} ],
               "priceTrail": "rule:TXT-SEASONAL-MARKDOWN;list:RETAIL" } ],
  "tenders": [ {"type":"CASH","amount":{"amount":"1000.0000","currency":"INR"}},
               {"type":"UPI","amount":{"amount":"33.3200","currency":"INR"},"reference":"UPI123"} ],
  "roundOff": {"amount":"-0.3200","currency":"INR"} }
```

Response: invoice id, number, totals, GL voucher id, print payload reference.

## 6. Purchasing, accounting, reports

```
POST /purchasing/orders | /purchasing/receipts | /purchasing/invoices | /purchasing/payments
GET  /purchasing/payables-ageing?asOf=
POST /accounting/vouchers                   (manual journal)
GET  /accounting/trial-balance?from=&to=&detailed=
GET  /accounting/statements/{pl|balance-sheet|day-book}?from=&to=
POST /accounting/periods/{id}:lock | :unlock
GET  /reports/templates | POST /reports/{templateId}:run  → async job
GET  /reports/jobs/{jobId}                  → status + download links
```

## 7. Sync

```
POST /sync/push   {nodeId, changes[], headHash}     → {acceptedSeq, rejected[], serverToken}
GET  /sync/pull?since=<token>&scope=<profile>       → {changes[], nextToken, hasMore}
POST /sync/ack    {appliedToken, conflicts[]}
GET  /sync/status?nodeId=
POST /nodes:pair  {pairingCode, nodeInfo}           → node identity + seed
```

## 8. Platform

```
GET/POST /platform/layouts | /platform/layouts/{id}/versions | :publish
GET/POST /platform/rules   | /platform/rules/{id}/versions   | :simulate | :activate
GET/POST /platform/notification-templates | /platform/subscriptions
GET      /platform/audit?actor=&entity=&from=&to=
GET      /platform/health | /platform/nodes | /platform/plugins
GET/POST /platform/settings
```

## 9. Rate limits and security headers

Login: 10/min/IP and 5/min/user. Sync: 60 batches/min/node. Reports: 10 concurrent jobs/tenant. All responses carry `Cache-Control: no-store` for business data, HSTS, `X-Content-Type-Options: nosniff`. Service integrations use client-credential tokens with explicit scopes and an allow-listed IP range where configured.

## 10. Contract governance

The OpenAPI document is generated in CI, diffed against the previous release, and a breaking change fails the build unless the version is bumped. Consumer-driven contract tests cover the JavaFX client, the mobile app and the sync agent.
