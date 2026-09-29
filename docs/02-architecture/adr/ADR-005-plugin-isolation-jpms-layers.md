# ADR-005 — Plugins as JPMS layers behind a narrow SPI

Status: ACCEPTED · Date: 2026-09-29 · Drivers: BR-053, RSK-4, security §7

## Context

Vertical features (textile matrix, paint mixing, hardware bundles, processing BOM, payment gateways, e-invoicing) must be optional and independently deliverable without forking the core.

Options: everything in core behind feature flags (bloat, coupling); OSGi (powerful, heavy, steep learning curve); JPMS module layers + `ServiceLoader` (native to Java 21, lightweight, no dynamic unload); separate processes (isolation, but latency and complexity for UI contributions).

## Decision

Plugins are JARs in `plugins/` loaded at startup into a **child JPMS module layer** whose parent exports only `erp.plugin.api`. Services are discovered with `ServiceLoader`. Each plugin has a manifest (id, version, required API version, requested extension points, signature). Plugins are enabled per tenant via an allow-list; unsigned plugins require an explicit development flag.

Dynamic unload/reload at runtime is **not** supported — enabling or disabling a plugin requires a restart of the node. This avoids classloader-leak complexity for a use case (changing verticals) that happens rarely.

## Consequences

**Positive**: strong compile-time boundary (core internals are not exported); third parties and implementers can extend without touching core; failures are attributable.

**Negative**: SPI must be designed carefully and versioned (semver; breaking changes require a major API version and a migration note); restart needed for plugin changes; JPMS strictness makes some libraries awkward inside plugins (must be modular or automatic modules).

**Guardrails**: API compatibility test suite; a reference plugin kept in the repo and built in CI to catch accidental SPI breaks; exception barrier around every SPI call that disables the offending plugin instead of failing the transaction, except where the plugin is the authoritative calculator (then the transaction fails loudly).
