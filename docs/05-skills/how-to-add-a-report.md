# Practice card — Add a report or document template

**When**: a new report, invoice/document format or label is required.
**Prerequisites**: SKL-022/2. Read `report-designer-design.md`.

## Decide first: template-only or new data source?

- **Template-only** (most cases): an existing named data source has the fields. No code, no release — build it in the designer and publish.
- **New data source**: the data is not exposed yet. That is a code change: register a `ReportDataSource` (core module or plugin) with declared parameters, field metadata and a required permission.

## Adding a data source (code)

1. Write the query against **projections**, not transactional tables (`data-architecture.md` §5). If no projection fits, add one — do not query `stock_movement` directly from a report.
2. Implement `ReportDataSource`: `id` (namespaced, e.g. `inventory.stockAgeing`), parameters with types and defaults, field metadata (name, type, format hint), `requiredPermission`, and a **streaming** `execute` (cursor-based; never materialise the full result).
3. Apply the caller's data scope (store/cost-centre) inside the query — the report layer will not do it for you.
4. Add a performance test against the Large fixture; the budget is in `report-designer-design.md` §8. Exceeding it fails CI.

## Building the template (designer)

1. Pick the data source; declare parameters and their prompts (dates default to the current period).
2. Lay out bands: report header, page header, group headers/footers, detail, summary. Group before you aggregate.
3. Use `format: money|qty|date|percent` rather than manual string formatting; money is right-aligned with tabular numerals.
4. Labels are message keys so the template works in every locale.
5. Preview with the Large fixture, not with three rows — column widths and page breaks fail at scale.
6. Set page setup per output: A4 for office, 58/80 mm for thermal, label language for labels. Bind the template to a printer profile role, not to a specific device.
7. Publish (versioned, audited). Built-in templates are read-only — "Save as" first.

## Documents (invoices, notes) specifics

- Statutory content is mandatory: tax breakup per component, HSN, supplier/customer identifiers, invoice number and date, place of supply (CMP-02).
- Thermal templates must fit the configured width in characters; test on the narrowest supported paper.
- Reprints are marked DUPLICATE and audited — do not remove that element from a copied template.

## Checklist

- [ ] Uses a projection-backed named data source with a permission
- [ ] Streams; passes the performance budget on the Large fixture
- [ ] Parameters validated; sensible defaults
- [ ] Money/qty/date formats via format hints; locale-safe labels
- [ ] Previewed at A4 and thermal width; page breaks sane
- [ ] Drill-through to documents where a number could be questioned
- [ ] Published with a version note; rollback tested
