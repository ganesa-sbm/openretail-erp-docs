# ADR-006 — Counter-prefixed document sequences

Status: ACCEPTED · Date: 2026-09-29 · Drivers: BR-027, CMP-01, RSK-2

## Context

Invoice numbers must be human-readable, gapless per series and unique, while counters may create invoices with no server to allocate numbers.

Options: server-allocated numbers (fails offline); pre-allocated blocks per counter (gaps when a block is abandoned); counter-prefixed independent sequences (unique, gapless within the series, but not a single global ascending sequence).

## Decision

Each counter owns its own series: `<storeCode>/<counterCode>/<fyCode>/<6-digit seq>`, e.g. `MAIN/C1/2627/000123`. The sequence is local, gapless and reset per financial year. Uniqueness is enforced by a composite unique key `(tenant_id, series, seq)`. Voided invoices keep their number and are marked void (CMP-01).

Internally, every document additionally carries a UUIDv7 `id` used by all relationships and sync; the human number is presentation and statutory only.

## Consequences

**Positive**: works fully offline with zero coordination; audit-friendly (a gap in a counter's series is a real anomaly worth investigating); trivially collision-free.

**Negative**: no single ascending number across the store — reports and tax exports must be series-aware; a counter re-seeded from a backup could re-issue numbers, so re-seeding forces a sequence high-water-mark fetch from the server and, failing that, a series-suffix bump (e.g. `C1B`), which is audited.

**Alternatives kept available**: tenants who require a single store-wide series can enable server-allocated numbering, accepting that billing stops when the server is unreachable; this is a setting, and the UI states the trade-off explicitly.
