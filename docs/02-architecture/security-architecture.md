# Security architecture

## 1. Threat model (summary)

| Asset | Threat | Control |
| --- | --- | --- |
| Cash and stock | Cashier fraud (voids, discounts, fake returns, un-billed sales) | RBAC limits, approval workflow, immutable audit, exception reports, drawer variance |
| Financial records | Silent tampering to hide shrinkage | Append-only ledger, hash-chained audit, period locking, no hard delete |
| Customer PII | Theft of the shop PC or a backup file | Column encryption, encrypted backups, keys outside the DB |
| Credentials | Shared/weak passwords, shoulder surfing | Argon2id, lockout, per-user PIN at counters bound to device, session timeout |
| LAN traffic | Sniffing on a flat shop network | TLS everywhere, store CA, no plaintext DB access from clients |
| Cloud channel | MITM, credential replay | Public CA + key pinning, short-lived tokens, mutual auth for sync agents |
| Rules/layouts | Malicious or accidental rule that grants 100% discount | Four-eyes activation, versioning, simulation, rollback, audit |
| Plugins | Untrusted code in the JVM | Signed plugin JARs, allow-list per tenant, narrow SPI, no reflective access to core internals |

## 2. Identity and authentication

- Users belong to a tenant; a user may be assigned to one or more stores.
- Primary auth: username + password (Argon2id, memory 64 MB, iterations tuned to ~250 ms on reference hardware).
- Counter auth: short PIN permitted **only** when the device is enrolled (device key stored in OS keystore) and the user has an active shift; PIN attempts are rate-limited (3 attempts → 60 s lockout, 10 → account lock).
- Optional TOTP second factor for Admin and Accountant roles; mandatory for cloud console access.
- Sessions: JWT access token (15 min) + refresh token (12 h, rotating, bound to device) in LAN/cloud mode; in-process session in standalone mode. Logout and role changes revoke refresh tokens via a revocation list synced to nodes.
- Offline authentication: the node caches a verifier (Argon2id hash + role snapshot) for users who logged in within the last N days (configurable, default 30) so counters can authenticate without the server; cached credentials expire and are wiped on node de-pairing.

## 3. Authorization (RBAC)

Model: `user → role(s) → permission(s)`, with a **data scope** attached to each assignment (tenant / store / counter / cost-centre).

Permission naming: `<module>.<entity>.<action>[.<qualifier>]`, e.g. `sales.invoice.void`, `inventory.adjustment.approve`, `pricing.discount.override.upto20`.

Reference roles:

| Role | Representative permissions |
| --- | --- |
| Owner/Admin | All, including user administration and rule activation |
| Store Manager | Approvals, adjustments, price overrides, reports; not user admin, not period lock |
| Cashier | POS billing, returns up to limit, own drawer; read-only product/customer |
| Accountant | Vouchers, journals, statements, period lock, tax exports; no POS, no price edit |
| Auditor | Read-only everything incl. audit log; no writes at all |
| Storekeeper | Receipts, transfers, stock-take entry; no pricing, no financials |
| Support | Health, logs, backup/restore, configuration; business data read masked (PII redacted) |

Enforcement points: application-service method annotations (`@RequiresPermission`) as the single authoritative check, plus UI hiding (convenience only) and API-layer checks. Data scope is applied as a mandatory predicate in repositories (Hibernate filters) so a missing check cannot leak another store's data.

Approval workflow: permissions can carry limits (`discount ≤ 10%`). Exceeding a limit creates an `ApprovalRequest` resolved by a user holding the approving permission — in-place (manager enters credentials), on another node, or asynchronously for non-blocking cases.

## 4. Transport security

- TLS 1.2+ (prefer 1.3), modern cipher suites only, HSTS on the web layer.
- Store CA generated at server install; client truststores seeded during pairing; rotation supported with dual-trust grace period.
- Sync agents authenticate with a node certificate or a node-bound token; cloud pins the expected public key.
- No database port exposed beyond the store subnet; counters use loopback only.
- Remote access for support is via VPN/SSH tunnel to the store server; the product never opens inbound WAN ports.

## 5. Application security controls

| Control | Implementation |
| --- | --- |
| Input validation | Domain invariants + JSON schema validation of UI payloads and API bodies |
| SQL injection | JPA/criteria or parameterised SQL only; dynamic report SQL forbidden — report designer produces a typed query model |
| XSS (Vaadin) | Server-side rendering with escaped text; no raw HTML from user data |
| CSRF | Vaadin built-in tokens; REST uses bearer tokens, not cookies |
| Deserialization | JSON only, no Java serialization across process boundaries |
| Secrets in logs | Central masking filter over known secret keys; logging of entities uses explicit toString allowlists |
| Dependency risk | SBOM (CycloneDX) per release, OWASP dependency-check in CI, prefer releases ≥ 7 days old |
| Rate limiting | Login, sync and report endpoints per node/user |
| Clock integrity | Node clock skew detected during sync; documents record both node time and server-observed time |

## 6. Audit

Append-only `audit_event` with a per-node hash chain: `hash_n = H(hash_{n-1} || canonical_json(event))`. Chain heads are exchanged during sync and stored in day-close and year-close snapshots, making retroactive edits detectable. Audit writes participate in the business transaction (an unauditable operation must fail). Events list: see `docs/01-brd/compliance-and-audit.md`.

## 7. Plugin trust

- Plugins are JPMS layers loaded from `plugins/` with a manifest declaring id, version, required core API version, permissions requested and signature.
- Signature verification against the tenant's allow-list; unsigned plugins load only when `erp.plugins.allowUnsigned=true` (development only, warned in UI and audited).
- Plugins see only `erp.plugin.api`; core internals are not exported.
- Plugin failures are contained: exceptions crossing the SPI boundary disable the plugin and raise an alert rather than taking down billing.

## 8. Secure development lifecycle

Threat-model review for any new external interface; mandatory code review; SAST (SpotBugs + security plugin), dependency scanning, secret scanning in CI; annual penetration-test checklist for the web/cloud layer; documented vulnerability disclosure and patch SLA (critical ≤ 7 days from availability of a fix).
