# Domain model design

## 1. Bounded contexts

| Context | Aggregates | Key invariants |
| --- | --- | --- |
| Catalogue | Product (root) with Variants, UOM conversions, TaxClass | A variant belongs to exactly one product; barcode unique per tenant |
| Pricing | PriceList, Promotion | Non-overlapping active price for the same (variant, customer group, qty slab, date) or deterministic precedence |
| Inventory | StockBatch, StockMovement, StockTake, Transfer, ProductionOrder | Movements never edited; batch qty derived; FEFO enforced for expiry-tracked items |
| Sales | PosSession, DrawerSession, SalesInvoice (root with lines + tenders), SalesReturn | Invoice total = Σ lines + tax − discount; Σ tenders = total; immutable after finalisation |
| Purchasing | PurchaseOrder, GoodsReceipt, PurchaseInvoice, DebitNote, Payment | Receipt qty ≤ ordered + tolerance; invoice matched within tolerance before posting |
| Accounting | Voucher (root with journal lines), Account, Period | Σ debit = Σ credit per voucher; no posting into a locked period |
| Party | Customer, Supplier (one Party with roles) | Credit limit enforced at sale; phone unique per tenant (soft, merge-able) |
| Identity | User, Role, Permission, ApprovalRequest | A permission check has a scope; approvals cannot be self-granted when four-eyes is on |
| Platform | RulePackage, Layout, ReportTemplate, NotificationTemplate, PluginRegistration, Setting | Versioned; activation audited |

Aggregate rule: a transaction modifies exactly one aggregate; cross-aggregate effects happen via domain events in the same transaction's outbox (posting a sale creates the invoice aggregate; stock movements and the voucher are created by handlers within the same DB transaction but through their own aggregates' factories).

## 2. Core value objects (`erp.core.kernel`)

```java
public record Money(BigDecimal amount, CurrencyCode currency) {      // scale 4, HALF_UP at presentation
    public Money add(Money o) { … } public Money multiply(Quantity q) { … }
}
public record Quantity(BigDecimal value, UomId uom) { }              // scale 3
public record TaxRate(BigDecimal percent) { }
public record DocumentNumber(String series, int sequence) { }
public record BusinessDate(LocalDate value) { }                      // explicit, never derived silently
public record NodeId(UUID value) { }
public record EntityId(UUID value) { }                               // UUIDv7
```

Money arithmetic never uses `double`; division always specifies scale and rounding; currency mismatch throws.

## 3. Selected aggregates

### 3.1 SalesInvoice

```java
public final class SalesInvoice {
    private final EntityId id;
    private final DocumentNumber number;       // counter-prefixed, ADR-006
    private final StoreId storeId; private final CounterId counterId;
    private final BusinessDate businessDate;
    private PartyId customerId;                // nullable (walk-in)
    private final List<InvoiceLine> lines;
    private final List<Tender> tenders;
    private InvoiceStatus status;              // DRAFT → FINALISED → (VOIDED)
    private Money subtotal, discountTotal, taxTotal, roundOff, grandTotal;

    public void addLine(PricedLine priced) { requireDraft(); … recalc(); }
    public void applyBillDiscount(DiscountDecision d, ApprovalToken token) { … }
    public void addTender(Tender t) { requireDraft(); … }
    public FinalisedInvoice finalise(Clock clock) {
        requireDraft();
        require(tenderTotal().equals(grandTotal), "TENDER_MISMATCH");
        require(!lines.isEmpty(), "EMPTY_INVOICE");
        status = FINALISED;
        raise(new SaleCompleted(this));       // → stock movements, voucher, loyalty, notification
        return new FinalisedInvoice(this);
    }
    public VoidResult voidInvoice(Reason r, ApprovalToken t) { … }  // creates reversal, never deletes
}
```

Pricing, tax and discount eligibility are computed **outside** the aggregate by `PricingService` (rule-backed) and passed in as decided values, keeping the aggregate deterministic and testable and keeping Drools out of the domain module.

### 3.2 StockBatch / StockMovement

```java
public final class StockBatch {
    EntityId id; VariantId variantId; String batchCode;
    LocalDate manufactureDate, expiryDate; Money mrp, landedCost;
    // On-hand is NOT a field: derived from movements.
}

public final class StockMovement {  // immutable
    EntityId id; VariantId variantId; EntityId batchId; LocationId locationId;
    Quantity signedQty; Money unitCost; MovementReason reason;
    DocumentRef source; BusinessDate businessDate; Instant occurredAt;
}
```

`StockService.issue(variant, qty, policy)` selects batches (FEFO/FIFO/manual) and emits negative movements; `receive(...)` creates or reuses a batch and emits positive movements. Negative stock is rejected by default; a tenant setting allows it with a mandatory reason and an exception report entry.

### 3.3 Voucher

```java
public final class Voucher {
    EntityId id; VoucherType type; DocumentNumber number; BusinessDate date;
    List<JournalLine> lines; DocumentRef source; VoucherStatus status;
    public void post(PeriodService periods) {
        require(periods.isOpen(date), "PERIOD_LOCKED");
        require(sumDebit().equals(sumCredit()), "UNBALANCED");
        status = POSTED;
    }
    public Voucher reverse(Reason r) { … }  // mirrored lines, linked to original
}
```

### 3.4 ProductionOrder

Consumes BOM components (issue movements, FEFO) and produces output with cost = Σ component cost + configured overhead allocation; posts to WIP and finished-goods accounts. Scrap/by-products are explicit lines.

## 4. Domain events

| Event | Payload | Consumers |
| --- | --- | --- |
| `SaleCompleted` | invoice snapshot | Stock, Accounting, Loyalty, Notification, Analytics, Sync |
| `SaleVoided` | invoice id, reason, actor | Stock, Accounting, Audit, Analytics |
| `GoodsReceived` | receipt snapshot | Stock, Accounting, Analytics |
| `PurchaseInvoicePosted` | invoice snapshot | Accounting, Payables |
| `PaymentRecorded` | payment snapshot | Accounting, Party ledger |
| `StockAdjusted` | movement list, reason, approval | Accounting, Audit, Analytics |
| `StockBelowReorder` | variant, on hand, reorder level | Notification, Purchasing suggestion |
| `BatchNearExpiry` | batch, days left | Notification, Pricing (markdown suggestion) |
| `DrawerClosed` | session, counted vs expected | Accounting, Audit, Notification |
| `PeriodClosed` | period, snapshot hash | Reporting, Audit, Sync |
| `RuleActivated` / `LayoutPublished` | version, approver | Audit, Sync, node cache invalidation |

Events are published after successful commit via a transactional outbox; handlers are idempotent (keyed by event id).

## 5. Posting rules (subsidiary → GL)

Posting is table-driven, not hard-coded per module:

| Source | Debit | Credit |
| --- | --- | --- |
| Cash sale | Cash/Bank/UPI clearing (per tender) | Sales revenue (per tax group), Output tax, Round-off |
| Credit sale | Accounts receivable (customer) | Sales revenue, Output tax |
| COGS on sale | Cost of goods sold | Inventory |
| Purchase receipt (invoiced) | Inventory, Input tax | Accounts payable (supplier) |
| Landed cost | Inventory | Expense clearing / payable |
| Stock adjustment − | Shrinkage expense | Inventory |
| Stock adjustment + | Inventory | Stock gain income |
| Sales return | Sales return, Output tax reversal | Cash/Customer; Inventory ↔ COGS reversed |
| Production | Finished goods inventory | WIP / component inventory, Overhead applied |
| Payment to supplier | Accounts payable | Cash/Bank |
| Receipt from customer | Cash/Bank | Accounts receivable |
| Drawer variance | Cash short/over expense or income | Cash |

Each row is a `posting_rule` with account resolution expressions (by tax group, tender type, product category, cost centre), validated at save time against the chart of accounts.

## 6. Invariants tested in the domain test suite

1. Invoice: tenders equal grand total; totals equal recomputation from lines.
2. Voucher: balanced; no posting into locked period; reversal mirrors exactly.
3. Stock: no movement without a source document; batch expiry respected under FEFO; on-hand never negative unless explicitly permitted.
4. Returns: returned quantity ≤ sold quantity per original line across all returns.
5. Numbering: gapless per series; voids retain numbers.
6. Approvals: an operation requiring approval cannot finalise without a valid, unexpired, non-self approval token.
