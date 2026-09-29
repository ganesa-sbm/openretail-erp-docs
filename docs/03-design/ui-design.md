# UI design (JavaFX client, Vaadin web, Lanterna fallback)

## 1. Design principles

1. **Keyboard first.** Every POS action has a function key; the mouse is optional (NFR-013).
2. **No blocking surprises.** Validation is inline; dialogs are dismissible with Esc; long operations are asynchronous with progress and never freeze the UI thread.
3. **Legible on cheap monitors.** 1366×768 baseline, 14 px base font, high contrast, no colour-only status (NFR-014, NFR-026).
4. **State is always visible.** Connection mode (Online / Local mode), pending sync count, counter/cashier, business date and drawer status are permanently on screen.
5. **One primary action per screen**, bottom-right, plus a keyboard equivalent.

## 2. Shell layout

```
┌───────────────────────────────────────────────────────────────┐
│ Store ▸ Counter C1 │ Cashier: Lakshmi │ 29-Sep-2026 │ ● Online │  ← status bar
├──────────┬────────────────────────────────────────────────────┤
│ Modules  │  Workspace (tabbed; payload-rendered screens)       │
│  Sell    │                                                     │
│  Stock   │                                                     │
│  Buy     │                                                     │
│  Books   │                                                     │
│  Reports │                                                     │
│  Admin   │                                                     │
├──────────┴────────────────────────────────────────────────────┤
│ F1 Help  F2 Search  F4 Hold  F8 Payment  F12 Day close │ toast │
└───────────────────────────────────────────────────────────────┘
```

Navigation is module → screen; screens open as tabs; at most 8 tabs, LRU-closed.

## 3. POS billing screen (hand-built, ADR-003 exemption)

Regions: customer strip (phone-first lookup, walk-in default), item entry line (scanner focus trap), line grid (code, name, batch/expiry when relevant, qty, rate, disc, tax, amount), totals panel, tender panel, peripheral status.

Key bindings: `F2` item search, `F3` customer, `F4` hold, `F5` recall, `F6` line discount, `F7` quantity, `F8` payment, `F9` return mode, `F10` reprint, `Ctrl+Del` void line (permission), `Esc` cancel field.

Latency design: scanner input is handled on a dedicated event path; item lookup is a local indexed query with an in-memory LRU cache of hot SKUs; pricing rules evaluate on a bounded worker with a 50 ms budget and a cached fallback; printing is queued asynchronously after the transaction commits so a printer jam never blocks the next customer.

Failure affordances: "Local mode" banner appears within 2 s of losing the server; printing failure offers retry/skip and the invoice remains reprintable; scale disconnection falls back to manual weight entry with an audit note.

## 4. Back-office screens

Rendered by the payload-driven engine: masters (product, party, price list), documents (PO, GRN, purchase invoice, journal), stock operations (stock-take, transfer, production), and admin (users, roles, rules, layouts, templates, backups, node health). Standard screen chrome: filter bar, results grid with column chooser and export, detail pane, action bar with permission-aware buttons, and an audit tab on every entity.

## 5. Vaadin web layer

Same information architecture and the same layout payloads, rendered by a Vaadin renderer. Intended for back-office and owner use on LAN/remote, not for high-speed billing (a simplified billing view exists for occasional use). Server-side rendering keeps thin clients cheap; Vaadin's push channel delivers approval requests and alerts live.

## 6. Lanterna terminal client (Phase 4)

Text UI covering: login, billing (scan/enter code, qty, tender, print), stock query, day close, and node health. Same application services, no payload engine — a fixed, compact screen set. Usable over SSH on a 1 GB RAM machine; intended for degraded operation and for very low-end hardware.

## 7. Mobile (Phase 3, Compose Multiplatform)

Two focused apps rather than a port of the ERP: **Stock-take** (scan, count, submit, offline queue) and **Owner dashboard** (sales, cash, margins, alerts). Both talk to the store server's REST API and hold a local cache.

## 8. Visual language

- Base: light theme with a dark variant; brand colour configurable per tenant.
- Semantic colours: success `#2E7D32`, warning `#ED6C02`, danger `#C62828`, info `#0277BD` — always paired with an icon and text.
- Density: comfortable (36 px rows) default, compact (28 px) toggle for power users.
- Typography: system UI font, 14/16/20/24 scale; tabular numerals in all money columns; money right-aligned with fixed decimals.
- Iconography: single open-licence icon set; every icon button has a tooltip and an accessible name.

## 9. Interaction standards

| Situation | Standard |
| --- | --- |
| Destructive action | Confirm dialog naming the object and consequence; requires typing nothing but must be keyboard-confirmable |
| Approval needed | Inline approval strip, not a modal — manager enters credentials without losing cart state |
| Long operation | Progress with cancel; UI stays usable; result as a toast + notification centre entry |
| Error | Human-readable message + error code + "copy details" for support; never a stack trace |
| Empty state | Explains what the screen is for and offers the primary action |
| Unsaved changes | Guarded navigation with save/discard/cancel |

## 10. Accessibility and localisation

Focus ring always visible; tab order follows visual order; all interactive elements reachable by keyboard; contrast ≥ 4.5:1; no information by colour alone. All strings in resource bundles with a lint rule failing the build on hardcoded UI text; layouts support RTL mirroring; date/number/currency formatting is locale-driven with an explicit per-tenant locale setting.
