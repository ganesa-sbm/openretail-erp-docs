# FS-01 — Inventory functional specification

Traces: BR-001..BR-012

## 1. Product and variant master

**FR-101** Create/edit a product with: code (unique per tenant, auto-generatable), name, short name for thermal print, category, brand, HSN/SAC, tax class, base UOM, purchase UOM + conversion, sale UOM + conversion, batch-tracked flag, expiry-tracked flag, serial-tracked flag, reorder level, reorder qty, shelf life days, active flag, image, `attributes` (plugin fields).

**FR-102** A product has one or more variants. Non-variant products get a single implicit variant. Variant axes (up to 3, e.g. size/colour/design) are defined per product; the matrix generator creates the cross-product with per-variant barcode, price delta and reorder level.

**FR-103** Barcodes: multiple barcodes per variant (manufacturer EAN + internal). Internal barcode generation uses a configurable scheme (EAN-13 with check digit, or Code128). Weight-embedded barcodes are parsed by a configurable mask (prefix, item code positions, weight/price positions, decimal places).

**FR-104** UOM conversions are per product (`1 CASE = 12 PIECE`, `1 DRUM = 20 LITRE`) with 3-decimal factors; sale in a non-base UOM converts at the point of transaction and stores both the entered and base quantity.

**FR-105** Deactivating a product is allowed with stock on hand but blocks new purchase/sale; deletion is prohibited once any movement exists (CMP-05).

## 2. Batches and expiry

**FR-110** Batch-tracked items require a batch on every inbound movement: batch code (auto `YYMMDD-nn` if not supplied), manufacture date, expiry date (required if expiry-tracked), MRP, landed cost.

**FR-111** Outbound movements for expiry-tracked items default to FEFO; FIFO for batch-tracked-without-expiry; manual batch selection requires `inventory.batch.selectManual` permission and is recorded.

**FR-112** Expired stock cannot be sold. It appears in an expired-stock report and must be written off or returned to supplier via an explicit document.

**FR-113** Near-expiry thresholds are configurable per category (e.g. 30/15/7 days) and raise `BatchNearExpiry` events (notification + optional markdown decision).

## 3. Stock movements

**FR-120** Every stock change is a movement row with a source document; there is no direct "set stock" operation. Opening stock is a document (`OPENING`) posted to an opening-equity/stock account.

**FR-121** Reasons: `PURCHASE`, `PURCHASE_RETURN`, `SALE`, `SALE_RETURN`, `TRANSFER_OUT`, `TRANSFER_IN`, `ADJUST_PLUS`, `ADJUST_MINUS`, `WRITE_OFF`, `PRODUCE`, `CONSUME`, `REVALUE`, `OPENING`.

**FR-122** Negative on-hand is rejected by default (`inventory.allowNegative=false`). When enabled, a mandatory reason is captured, the event is listed in an exception report, and valuation uses the last known cost.

**FR-123** Locations and bins: stock is tracked per location; bins are an optional second level. Transfers between locations create paired out/in movements with an in-transit location when the transfer is multi-step (BR-007).

## 4. Valuation

**FR-130** Methods: weighted average (default) or FIFO/FEFO-by-batch. Set per tenant at go-live; change requires a period close and an explicit revaluation run.

**FR-131** Landed costs update batch cost either at receipt or via a later `REVALUE` movement, with the GL difference posted to inventory and the cost-variance account.

**FR-132** Stock value report reconciles to the inventory control account in the GL; any difference is reported by the nightly reconciliation job.

## 5. Stock-take

**FR-140** Create a stock-take with a scope (all / category / location / brand / ABC class), mode (blind or informed), and a freeze option that blocks movements in scope until completion.

**FR-141** Count entry by scanner or sheet, multiple counters entering in parallel, recount support for lines with variance beyond a threshold.

**FR-142** Approval by a user with `inventory.stocktake.approve` posts `ADJUST_PLUS`/`ADJUST_MINUS` movements and the GL entries, with a mandatory reason per line above a value threshold.

**FR-143** Stock-take variance report per counter, category and value, plus a history of takes for shrinkage trend analysis.

## 6. Processing / assembly

**FR-150** BOM per output variant with version and validity dates; components with quantity per unit, scrap %, and optional substitutes.

**FR-151** Production order: planned quantity, component issue (FEFO), actual output with its own batch, scrap/by-product lines, and overhead allocation (fixed amount, % of component cost, or per-unit rate).

**FR-152** Output cost = Σ (component qty × cost) + overhead − by-product credit. Posting: credit component inventory, debit WIP, then credit WIP, debit finished goods.

**FR-153** Paint mixing (plugin) records base + tint formula per sale or per batch and consumes components accordingly; hardware kitting uses the same BOM mechanism.

## 7. Alerts and exceptions

**FR-160** Reorder alert when on-hand (± incoming POs) < reorder level; produces a suggested purchase list grouped by supplier.

**FR-161** Slow-moving/dead-stock report by configurable age buckets and last-movement date.

**FR-162** Exception reports: negative-stock events, manual batch selections, adjustments above value threshold, cost corrections, expired-stock value.

## 8. Screens

`Product list/edit`, `Variant matrix`, `Batch browser`, `Stock on hand`, `Stock movements (ledger)`, `Stock-take`, `Transfer`, `Adjustment`, `Production order`, `BOM`, `Label printing`, `Reorder suggestions` — all payload-rendered except the label print preview.

## 9. Acceptance criteria (samples)

| ID | Given | When | Then |
| --- | --- | --- | --- |
| AC-101 | Item is expiry-tracked with batches expiring 10-Oct and 20-Oct | 5 units are sold | The 10-Oct batch is issued first; movements reference that batch |
| AC-102 | On-hand is 3 and negative stock is disallowed | A sale of 5 is attempted | Sale is blocked with `INSUFFICIENT_STOCK` naming the item and available qty |
| AC-103 | A stock-take counts 48 against a system 50 | The take is approved | Two `ADJUST_MINUS` units are posted with a shrinkage GL entry and an audit event |
| AC-104 | A PO of 100 units arrives as 60 then 40 | Both GRNs are posted | Stock reflects 100; PO is closed; three-way match succeeds within tolerance |
| AC-105 | Weight-embedded barcode `2100123004500` with the store mask | Scanned at POS | Item `00123` at 0.450 kg is added with correct price |
