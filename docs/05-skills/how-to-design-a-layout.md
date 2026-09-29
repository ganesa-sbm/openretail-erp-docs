# Practice card — Design or modify a screen layout (payload-driven UI)

**When**: a screen needs different fields, order, defaults or a vertical-specific variant.
**Prerequisites**: SKL-013/2. Read `payload-driven-ui-design.md` and `TS-03-ui-payload-schema.md`.
**Boundary**: layouts arrange and bind the **existing component catalogue** to an **existing data context**. New component types and new bound fields are code changes.

## Steps

1. **Start from the published core layout** — "Save as draft" creates a tenant-scoped override; the core layout stays the upgrade baseline.
2. **Check the data context** for the fields you need. Missing field → request a data-context extension (code) or use a plugin custom attribute.
3. **Compose** with catalogue components only. Keep the keyboard order sane: `tabIndex` follows visual order; the first field is focused on open.
4. **Bind** with `binding: "invoice.lines[].qty"` style paths; use the restricted expression language for `visibleWhen`/`enabledWhen`/`requiredWhen`. Expressions are side-effect free and must evaluate in well under the frame budget — no heavy computation.
5. **Validate** declaratively (required, range, regex, decimals). Business validation stays server-side; the layout's job is early feedback, not enforcement.
6. **Actions** reference declared action ids only; a layout cannot invent behaviour.
7. **POS screens** obey extra rules: no modal that can trap a cashier mid-bill, every action reachable by a key, nothing that blocks the UI thread, and the layout must still render on the smallest supported resolution.
8. **Preview** in the designer against sample data, then on a real counter resolution. Check FX and, if the tenant uses it, Vaadin — both renderers consume the same payload but line-wrap differently.
9. **Validate and publish**: publication runs schema validation, binding resolution, action existence, cycle detection and an accessibility check (labels, contrast, focus order). Failures block publication.
10. **Version and roll back**: publishing creates a new version with an audit entry; rollback restores the previous version instantly.

## Upgrade safety

A tenant override pins the core version it derives from. When core changes that screen, the admin sees a three-way diff and chooses to merge or keep. Plugin contributions are **patches** (insert/replace/remove at an anchor), which re-apply cleanly across upgrades; whole-screen replacements do not — avoid them.

## Checklist

- [ ] Catalogue components only; no new types invented
- [ ] All bindings resolve; all actions exist
- [ ] Keyboard order and focus verified; POS fully keyboard-operable
- [ ] Declarative validation present; server validation not bypassed
- [ ] Labels are message keys; passes the accessibility check
- [ ] Previewed on target resolution and on every renderer in use
- [ ] Version note recorded; rollback tested
