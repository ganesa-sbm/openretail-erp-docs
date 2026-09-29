# Integration architecture

## 1. Peripheral integration (store hardware)

| Device | Interface | Approach |
| --- | --- | --- |
| Barcode scanner | USB HID keyboard wedge | Captured as keystrokes with prefix/suffix and inter-key timing heuristics; scanner profile per counter |
| Weighing scale | Serial/USB CDC, or weight-embedded barcode | jSerialComm adapter with protocol profiles (Avery, Essae, generic 8-byte ASCII); embedded-barcode parser configurable by mask |
| Thermal printer | ESC/POS over USB/serial/network | Direct ESC/POS byte streams (no OS driver dependency) via a printer profile; fallback to javax.print |
| A4 printer | OS print service | PDF render → javax.print |
| Label printer | ZPL/TSPL | Template rendered to printer language; label designer produces the template |
| Cash drawer | Kick pulse via printer | ESC/POS drawer-kick command |
| Customer display | Serial VFD | Line protocol adapter |
| Card/UPI terminal | Vendor SDK or manual | Phase 1: manual reference entry; Phase 2+: plugin per vendor |

All peripherals sit behind `erp.client.fx` port interfaces (`ReceiptPrinter`, `WeighingScale`, `CashDrawer`, `CustomerDisplay`) with a mock implementation used in tests and in the TUI client.

## 2. External services

| Service | Direction | Contract |
| --- | --- | --- |
| SMS gateway | Outbound | `SmsSender` SPI; adapters for generic HTTP/REST gateways, configurable request template so a shop can plug any local provider without code |
| Email (SMTP) | Outbound | Jakarta Mail, TLS, per-tenant credentials encrypted |
| Payment/UPI provider | Outbound | Plugin; core only records a tender with a reference and status |
| Tax e-invoicing portal | Outbound | Plugin; core produces a canonical invoice payload and stores the acknowledgement |
| Bank statement | Inbound | CSV/MT940 import into reconciliation staging |
| Supplier price list / invoice | Inbound | CSV/XLSX import with a mapping profile per supplier |
| Accounting package export | Outbound | Tally XML / CSV export templates |
| Object storage (backups) | Outbound | S3-compatible API (MinIO, any provider) |

## 3. Public API

The store server exposes a versioned REST API (`/api/v1`) used by the JavaFX client, the Vaadin layer, the mobile app and third parties:

- OpenAPI 3 document generated from controllers and published at `/api/openapi.json`.
- Authentication: JWT bearer; service integrations use client-credential tokens with scoped permissions.
- Idempotency: `Idempotency-Key` header required on all POSTs that create documents.
- Pagination: cursor-based (`?cursor=&limit=`) for large collections.
- Errors: RFC 7807 `application/problem+json` with a stable `code` field.
- Versioning: additive changes only within `v1`; breaking changes require `v2` with an overlap window (NFR-022 / deployment §8).

## 4. Import/export framework

A single import pipeline is reused by all inbound files: upload → profile (column mapping, transformations) → dry-run validation report → staged rows → commit as domain operations (never direct SQL) → import audit record with row-level results and a downloadable error file. Export mirrors it: query model → renderer (CSV/XLSX/PDF/XML) → audit record (PII exports require permission, DP-05).

## 5. Event integration

Domain events (`SaleCompleted`, `StockAdjusted`, `PurchaseReceived`, `PaymentReceived`, `StockBelowReorder`, `BatchNearExpiry`, `PeriodClosed`, …) are published in-process and written to a transactional outbox. Consumers: notification service, analytics projections, plugins, and optionally an outbound webhook dispatcher (`POST` with HMAC-SHA256 signature, retries with exponential backoff, dead-letter after 24 h) for shops that want to wire the ERP to something else.

## 6. Plugin extension points (integration-relevant)

`DocumentNumberingStrategy`, `TaxCalculator`, `PriceResolver`, `PaymentProcessor`, `NotificationChannel`, `ImportProfileProvider`, `ExportFormat`, `ReportDataSource`, `PeripheralDriver`, `ScreenContribution`. Full SPI in `docs/04-specifications/plugin-spi-spec.md`.
