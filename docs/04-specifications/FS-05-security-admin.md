# FS-05 — Security and administration functional specification

Traces: BR-070..BR-077, security architecture

## 1. Users

**FR-501** User: username (unique per tenant), display name, email/phone, password, status (active/locked/disabled), roles with scopes, allowed counters, PIN (optional, device-bound), TOTP secret (optional/required by role), password-expiry policy, last login, failed-attempt counter.

**FR-502** Password policy per tenant: minimum length (default 10), complexity or passphrase mode, reuse prevention (last 5), lockout after N failures (default 5) for M minutes (default 15). Admin reset forces change at next login.

**FR-503** Cashier PIN login requires an enrolled device and an active shift; PIN never substitutes for password on admin/accounting roles.

## 2. Roles and permissions

**FR-510** Roles are tenant-editable; the shipped set (Owner, Manager, Cashier, Accountant, Auditor, Storekeeper, Support) can be cloned but the Auditor role is read-only by construction and cannot be granted write permissions.

**FR-511** Permission assignment carries a scope (tenant/store/counter/cost-centre) and optional limits (discount %, adjustment value, refund value).

**FR-512** A permission matrix screen shows roles × permissions with search, diff-versus-default, and export; every change is audited with before/after.

**FR-513** Enforcement is server-side at the application-service boundary; UI hiding is presentation only. Repository-level scope predicates prevent cross-store data access.

## 3. Approvals

**FR-520** An operation exceeding a limit creates an `ApprovalRequest` (operation, context snapshot, requester, required permission, expiry). Resolution: inline credential entry, another node, or asynchronous.

**FR-521** Self-approval is blocked when four-eyes is enabled for that operation. All grants/denials are audited with both actors, the context and the reason.

## 4. Audit

**FR-530** Append-only audit log with the event list from `compliance-and-audit.md`, per-node hash chain, and an audit browser with filters (actor, entity, event type, date, store), field-level before/after diff, and CSV/JSON export (permission-gated).

**FR-531** The audit log cannot be modified or deleted through any application path; retention follows CMP-04. Chain verification runs nightly and on demand, reporting any break.

## 5. Settings

**FR-540** Layered settings (system default → tenant → store → node → user) with an admin UI showing effective value and origin, validation per setting type, and audit on change. Settings are synced (node-scoped ones excluded).

**FR-541** Settings groups: company/tax identity, financial year, numbering, tax model, valuation, POS behaviour, printing, notifications, security policy, backup, sync, plugins, localisation.

## 6. Backup and restore

**FR-550** Scheduled backups (Quartz) per deployment §7 with target selection (local path, second disk, network share, S3-compatible), encryption, retention, and integrity verification (checksum + weekly test restore).

**FR-551** Restore wizard: choose backup, verify integrity, show contents (tenant, date range, version), warn about data loss, require owner credentials, run, then run reconciliation and report.

**FR-552** Export/import of configuration (chart of accounts, settings, layouts, rules, templates, price lists) as a portable bundle for implementer workflows.

## 7. Node and health administration

**FR-560** Node registry: id, role, store, counter, version, status, last seen, last sync, pending changes, conflicts, backup age, disk free, plugin list. Pairing and de-pairing flows (deployment §4); de-pairing wipes cached credentials and requires the node's unsynced changes to be pushed or explicitly discarded (audited).

**FR-561** Health screen with traffic-light status and the alert rules from deployment §9; a one-click diagnostic bundle (logs, config with secrets redacted, schema version, recent errors) for support.

**FR-562** Conflict queue UI: list, field-level diff, resolve (keep local/keep remote/merge), bulk resolve for identical cases, full audit of resolutions.

## 8. Plugin administration

**FR-570** Plugin list with id, version, status, signature validity, tenant enablement, required core API, and last load diagnostics; enable/disable with a restart prompt; uninstall with a data-retention choice.

## 9. Acceptance criteria (samples)

| ID | Given | When | Then |
| --- | --- | --- | --- |
| AC-501 | Cashier role lacks `pos.invoice.void` | Cashier attempts a void via the API directly | Rejected 403 with problem code `PERMISSION_DENIED`; attempt is audited |
| AC-502 | Four-eyes enabled for rule activation | Rule author tries to activate their own rule | Blocked; a second approver can activate; both recorded |
| AC-503 | Audit row is altered directly in the database | Nightly chain verification runs | Break is detected and reported with the affected sequence range |
| AC-504 | Manager is scoped to Store A | They open the stock report | Only Store A data is returned even if a Store B id is supplied explicitly |
| AC-505 | Backup target is full | Nightly backup runs | Failure is recorded, alert raised after the second failure, business operations unaffected |
