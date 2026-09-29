# TS-03 — UI layout payload schema specification

Companion to `docs/03-design/payload-driven-ui-design.md`.

## 1. Envelope

```json
{
  "$schema": "https://openretail/schemas/layout-1.json",
  "layoutId": "string (namespaced, e.g. purchase.receipt)",
  "version": 1,
  "title": "message key or literal",
  "dataContext": "registered context id",
  "permissions": ["optional screen-level permissions"],
  "root": { "…component…" },
  "meta": { "author": "", "notes": "", "basedOn": 6 }
}
```

## 2. Component schema (common fields)

| Field | Type | Notes |
| --- | --- | --- |
| `type` | enum | From the catalogue; unknown type = validation error |
| `id` | string | Unique within the layout; required for focusable components |
| `bind` | path | Must resolve in the declared data context |
| `label` | string | Message key preferred; literal allowed |
| `props` | object | Type-specific, schema-validated per type |
| `children` | array | Containers only |
| `visibleWhen` / `enabledWhen` / `requiredWhen` | expression | Restricted expression language (§4) |
| `validators` | array | `{type, params, message}` |
| `shortcut` | string | e.g. `F3`, `Ctrl+S`; must not collide within a screen |
| `permissions` | array | Component is hidden if the user lacks any |
| `style` | object | Only `width`, `minWidth`, `align`, `density`, `emphasis` — no free CSS |

## 3. Component catalogue and props

| Type | Key props |
| --- | --- |
| `column` / `row` | `gap`, `align`, `wrap` |
| `section` | `collapsible`, `collapsedByDefault` |
| `tabs` | `children` of type `tab` (`label`, `children`) |
| `label` | `text`, `emphasis` |
| `textField` | `maxLength`, `placeholder`, `mask`, `uppercase` |
| `numberField` | `scale`, `min`, `max`, `step` |
| `moneyField` | `currencyFrom` (path), `allowNegative` |
| `dateField` | `min`, `max`, `defaultToday` |
| `checkbox` | — |
| `comboBox` | `options` (static) or `optionsQuery` (named query), `allowFreeText` |
| `lookupField` | `query`, `minChars`, `displayField`, `createAction` |
| `barcodeField` | `scannerProfile`, `onScanAction` |
| `grid` | `columns[]`, `footer[]`, `editable`, `addAction`, `removeAction`, `rowHeight`, `selectionMode` |
| `matrixGrid` | `rowAxis`, `colAxis`, `cellBind`, `cellType` |
| `summaryPanel` | `items[] {label, bind, format, emphasis}` |
| `chart` | `query`, `chartType` (line/bar/pie), `x`, `series[]` |
| `actionBar` | `children` of type `action` (`action`, `primary`, `confirm`, `permissions`, `shortcut`) |
| `statusBadge` | `bind`, `mapping {value: severity}` |
| `pluginSlot` | `slotId` — extension anchor (plugin design §3) |

## 4. Expression language (restricted)

Grammar: literals (string, number, boolean, null), paths (`row.x`, `header.y`, `params.z`, `user.permissions`, `ctx.mode`), operators `= != < <= > >= && || !`, `in`, functions `isEmpty(p)`, `length(p)`, `sum(path)`, `today()`. No method invocation, no assignment, no loops, no I/O. Max 40 nodes per expression; evaluation is pure and must complete in < 1 ms.

## 5. Validators

`required`, `minLength`, `maxLength`, `pattern`, `min`, `max`, `scale`, `afterToday`, `beforeToday`, `dateRange`, `uniqueInGrid`, `custom:<registeredValidatorId>`. Each carries a `message` (message key). Presentation validators never replace domain invariants.

## 6. Patches (plugins and tenant overrides)

JSON Merge Patch with array handling by `id`:

```json
{ "layoutId": "catalogue.product", "patchId": "textile.fabric",
  "target": { "slotId": "product.extraSections" },
  "insert": { "type": "section", "label": "Fabric", "children": [
    { "type": "textField", "bind": "attributes.textile.fabricType", "label": "Fabric" },
    { "type": "numberField", "bind": "attributes.textile.gsm", "label": "GSM", "props": {"scale":0} }
  ]}}
```

Precedence: product default → plugin patches (load order) → tenant override → user preference. Conflicting patches on the same `id` produce a load-time warning and last-writer-wins by precedence.

## 7. Validation and publication

Publish-time checks: JSON Schema, binding resolution against the data-context metadata, action existence, permission existence, shortcut collisions, budget limits (≤ 120 bound components, ≤ 3 grid nesting levels, ≤ 40 conditions), and a render smoke test in a headless renderer. Failure blocks publication with a precise error path (`root.children[2].columns[3].bind`).

## 8. Runtime contract

Renderers must: apply `visibleWhen`/`enabledWhen` reactively on data-context change; preserve focus and caret across re-evaluation; never re-create nodes for value-only changes; virtualise grids beyond 50 rows; report a render error as an inline banner naming the component id rather than a blank screen.

## 9. Versioning

Schema version is in `$schema`. A renderer supports the current major schema and one previous. Layouts are stored with the schema version; an upgrade migrates stored layouts in a Flyway data migration with an automated transformation and a validation report.
