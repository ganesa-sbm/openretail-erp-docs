# TS-04 — Plugin SPI specification

Module: `erp.plugin.api` · Semver · Current: `1.0.0`

## 1. Lifecycle interface

```java
public interface ErpPlugin {
    PluginInfo info();
    /** Called once after the layer is created, before any extension is used. */
    void initialize(PluginContext ctx) throws PluginException;
    /** Called on orderly shutdown; must not block more than 5 seconds. */
    default void shutdown() {}
}

public interface PluginContext {
    TenantId tenant();
    Settings settings();                 // namespaced to the plugin
    Services services();                 // published application services (read/write via use cases)
    EventPublisher events();
    Resources resources();               // layouts, rules, reports bundled in the JAR
    Logger logger();
}
```

`Services` exposes only interfaces from `erp.plugin.api.services` (`CatalogueQuery`, `StockQuery`, `StockCommand`, `SalesQuery`, `AccountingCommand`, `PartyQuery`, …). Direct repository, `EntityManager` or JDBC access is not available.

## 2. Extension points

```java
public interface PriceResolver {
    int priority();                                   // higher wins; core default = 0
    Optional<PriceDecision> resolve(PriceFacts facts); // empty = defer to next resolver
}

public interface TaxCalculator {
    boolean supports(Jurisdiction j);
    TaxDecision calculate(TaxFacts facts);
}

public interface DocumentNumberingStrategy {
    boolean supports(DocumentType type);
    DocumentNumber next(NumberingContext ctx);        // must be collision-free per node
}

public interface PaymentProcessor {
    TenderType tenderType();
    PaymentResult authorize(PaymentRequest req);      // timeout enforced by core
    PaymentResult refund(RefundRequest req);
    PaymentResult status(String providerRef);
}

public interface NotificationChannel {
    String channelId();
    DeliveryResult send(RenderedMessage message);
    default boolean supportsDeliveryReceipt() { return false; }
}

public interface ScreenContribution {
    List<LayoutResource> layouts();                   // new screens
    List<LayoutPatch> patches();                      // patches into pluginSlots
    List<MenuEntry> menuEntries();
}

public interface ReportDataSource {
    String id();
    List<ParameterSpec> parameters();
    FieldMetadata fields();
    String requiredPermission();
    RowStream execute(Map<String,Object> params, Scope scope);   // must stream, not materialise
}

public interface PeripheralDriver<T extends Peripheral> {
    String driverId();
    Class<T> peripheralType();
    T open(DeviceConfig config) throws DeviceException;
}

public interface ValidationContributor {
    DocumentType appliesTo();
    List<ValidationMessage> validate(DocumentSnapshot doc);   // BLOCK or WARN
}

public interface EntityAttributeSchema {
    String entity();                 // "product", "party", …
    String namespace();              // "textile"
    String jsonSchema();             // validated on save, bindable in layouts
}

public interface DomainEventListener {
    Set<Class<? extends DomainEvent>> interestedIn();
    void on(DomainEvent event);      // idempotent; runs post-commit
}

public interface ImportProfileProvider { List<ImportProfile> profiles(); }
public interface ExportFormat { String id(); void write(RowStream rows, OutputStream out); }
```

All are discovered via `ServiceLoader` (`provides X with Y` in `module-info.java`).

## 3. Contract rules

1. **Purity and thread-safety**: implementations must be stateless or internally synchronised; core may call them concurrently.
2. **Timeouts**: every SPI call has a core-enforced timeout (price/tax 50 ms, validation 100 ms, payment 60 s, report streaming per-row 10 ms budget). Exceeding it aborts the call and applies the fallback.
3. **No I/O in hot-path SPIs** (`PriceResolver`, `TaxCalculator`, `ValidationContributor`) except in-memory lookups.
4. **Exception policy**: a thrown exception from a non-authoritative SPI is logged, alerted, and the core default applies; repeated failures (5 in 5 min) quarantine the plugin. For authoritative SPIs (`TaxCalculator` for the active jurisdiction, `PaymentProcessor` for the chosen tender), the transaction fails with a clear error instead of silently mis-posting.
5. **No core mutation**: plugins mutate state only via `Services` use cases, which enforce permissions, validation and audit.
6. **Data ownership**: plugin tables live in the plugin's own schema; core entities are extended through `attributes` with a declared schema.

## 4. Manifest (`plugin.json`) schema

```json
{ "id": "reverse-dns id", "name": "string", "version": "semver",
  "coreApi": "semver range", "vendor": "string", "licence": "SPDX id",
  "extensionPoints": ["…"], "schema": "db schema name or null",
  "permissions": ["new permissions declared by the plugin"],
  "settings": [ {"key":"…","type":"string|int|bool|money|enum","default":"…","label":"…"} ],
  "dependsOn": [ {"id":"…","version":"range"} ],
  "signature": "detached signature file name" }
```

## 5. Packaging and verification

- JPMS-modular JAR with `module-info.java` requiring only `erp.plugin.api` and JDK modules; third-party libraries must be shaded into the plugin JAR or declared as modular dependencies shipped alongside.
- Signed with the vendor key; the tenant allow-list maps plugin id → accepted key fingerprints.
- The core refuses to load a plugin whose `coreApi` range excludes the running core version, whose signature fails, or whose declared permissions are not approved for the tenant.

## 6. Compatibility policy

| Change | Allowed in |
| --- | --- |
| Adding a new SPI interface or a default method | Minor |
| Adding a field to a fact/decision record (with a default) | Minor |
| Removing/renaming an SPI method or type | Major only |
| Tightening a timeout or a call contract | Major (or minor with a deprecation window of one minor release) |

An API compatibility test module (`erp-plugin-api-compat`) and a reference plugin built in CI guard these rules.

## 7. Reference plugin skeleton

```java
module com.openretail.plugin.textile {
    requires erp.plugin.api;
    provides ErpPlugin with com.openretail.plugin.textile.TextilePlugin;
    provides ScreenContribution with com.openretail.plugin.textile.TextileScreens;
    provides PriceResolver with com.openretail.plugin.textile.SeasonalPriceResolver;
    provides EntityAttributeSchema with com.openretail.plugin.textile.FabricAttributes;
}
```

Testing: plugins get a `PluginTestKit` with an in-memory `PluginContext`, fake services and assertion helpers, so a plugin can be tested without booting the ERP.
