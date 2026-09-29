# Practice card — Build a plugin

**When**: vertical-specific behaviour (textile size/colour matrices, paint tinting, processing yields), a regional statutory integration, or a payment/peripheral driver.
**Prerequisites**: SKL-002/3, SKL-003/3. Read `plugin-framework-design.md` and `TS-04-plugin-spi-spec.md`.
**Rule of thumb**: if it changes the ledger's meaning, it is not a plugin.

## Steps

1. **Scaffold** with `./gradlew newPlugin -Pid=com.acme.textile`. You get a Gradle subproject, `module-info.java` requiring only `erp.plugin.api`, a `plugin.json`, and a test harness.
2. **Declare metadata** in `plugin.json`: id, version, display name, `coreApiVersion` range, required permissions, owned schema name, extension points used. A plugin requesting permissions it does not use fails review.
3. **Implement `ErpPlugin`**: `info()`, `initialize(PluginContext)` (register contributions, nothing slow — no network calls, no migrations run inline), `shutdown()` (release devices, threads, files; leaks here break hot reload).
4. **Implement extension points** you need — `PriceResolver`, `TaxCalculator`, `DocumentNumberingStrategy`, `PaymentProcessor`, `NotificationChannel`, `ScreenContribution`, `ReportDataSource`, `PeripheralDriver`, `ValidationContributor`, `EntityAttributeSchema`, `DomainEventListener`. Register them in `module-info.java` with `provides ... with ...`.
5. **Own your data**: plugin tables live in the plugin's schema with its own Flyway location. Never alter core tables; extend entities through `EntityAttributeSchema` (typed custom attributes) instead.
6. **Contribute UI** as layout patches (`TS-03` §7), not by replacing core screens — patches survive core upgrades, replacements do not.
7. **Handle absence**: core must work with the plugin uninstalled, and the plugin must degrade if a device or remote service is down (queue, retry, surface a clear status).
8. **Sync**: plugin tables must declare a conflict policy exactly like core tables, or they will not replicate.
9. **Test** with the plugin harness: load/unload cycles (check for leaks), core-version compatibility at both ends of the declared range, uninstall leaving core functional, and upgrade with existing plugin data.
10. **Sign** the JAR. Unsigned plugins load only when the node is explicitly in developer mode.

## Review checklist

- [ ] Only `erp.plugin.api` in `requires` (no reach-through into core internals)
- [ ] `coreApiVersion` range accurate and tested at both bounds
- [ ] Permissions minimal and actually used
- [ ] Own schema; zero changes to core tables; migrations idempotent
- [ ] UI via patches; layouts validate
- [ ] Clean `shutdown()`; no leaked threads/handles (harness asserts)
- [ ] Sync policy declared for every plugin table
- [ ] Signed; licence compatible and recorded in the SBOM
