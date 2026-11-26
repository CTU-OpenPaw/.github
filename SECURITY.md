# Security Policy / Chính sách Bảo mật (CTU-OpenPaw)

This organization-wide Security Policy applies across all **CTU-OpenPaw** repositories:
`CTU-OpenPaw/.github`, `CTU-OpenPaw/lattice-dx`, `CTU-OpenPaw/lattice-docs`, and `CTU-OpenPaw/lattice-templates`.

---

## 1. Supported Versions / Phiên bản được hỗ trợ

CTU-OpenPaw enforces a strict pre-major `0.x` version policy (`0.4.2` current baseline; major release transitions require explicit maintainer approval).

| Version / Branch | Repositories | Support Status |
| --- | --- | --- |
| `0.4.2` (`main`) | `lattice-dx`, `lattice-docs`, `lattice-templates`, `.github` | Actively supported with security updates / Được hỗ trợ vá lỗi bảo mật |
| `< 0.4.2` (`<= 0.4.1`) | `lattice-dx` | Upgrade to `0.4.2` / Khuyến nghị nâng cấp lên `0.4.2` |

---

## 2. Implemented Security Controls / Ranh giới bảo mật đã triển khai

### Identity, Access & Multi-Tenant Isolation
- **OIDC + PKCE S256 (`@latticedx/plugin-enterprise-auth`):** Authorization Code + PKCE S256 authentication against Keycloak (`26.8.2`), Valkey `8.1` session cache, backchannel logout, and two-way role sync. Core Fastify API (`@dx/api`) verifies signed JWTs (`OIDC_ISSUER` / `OIDC_JWKS_URL`), `organization_id`, and optimistic concurrency (`expectedVersion`).
- **PostgreSQL 16 `FORCE RLS`:** `56` static tenant-scoped tables (plus dynamic `latticeCustom*` tables) enforce `ENABLE` and `FORCE ROW LEVEL SECURITY` (`lattice_tenant_isolation` policy) under non-superuser database role `nocobase_app` via transaction-scoped `set_config('app.current_org', ..., true)`.
- **Scoped PaaS API Keys & Ed25519 PKI Ledger:** API keys (`ldx_pat_*`) are SHA-256 hashed at rest with explicit scopes (`read`, `write`, `wms`, `dmn`, `iot`, `ai`, `ledger`) and expiration (`1..365` days). Audit ledger entries and public tracking documents use Ed25519 signatures (private keys encrypted with AES-256-GCM) chained with Merkle roots.

### File Scanning, AI Guardrails & Supply Chain Hygiene
- **Antivirus Quarantine (`@dx/file-scanner` + ClamAV `1.5.5`):** Uploaded files stored in RustFS S3 are scanned over TCP via **`clamav/clamav:1.5.5`** before download or processing.
- **CVE & Dependency Hygiene:** Production dependencies are audited continuously via `pnpm audit --prod --audit-level=low` (`0` known vulnerabilities) and tracked in a verified CycloneDX 1.5 SBOM (`257/257` resolved components, `0` `NONE` licenses, `0` `BSL-1.1`).
- **Immutable Container Digests & Secret Hygiene:** Every OCI container image in `infra/compose/*.yml` is pinned by immutable `@sha256:` digest. AI observability traces redact JWTs, Bearer tokens, `sk-*`, `ldx_pat_*`, emails, and Vietnamese PII (CCCD/CMND, phone numbers, bank accounts, tax IDs).
- **Zero Secrets Policy:** Never commit `.env`, API tokens, private keys (`*.pem`, `*.key`), or production credentials. Automated checks (`pnpm pof:check` and Trivy `vuln,misconfig,secret`) enforce secret hygiene across commits and PRs.

---

## 3. Local Loopback Fixture Warning / Cảnh báo Fixture cục bộ

- Default local testing compose overlays and quickstart guides use intentionally public **loopback fixture credentials** strictly for `127.0.0.1` / `localhost` evaluation (e.g., `local-demo-password`, `local-admin-password`, `local-nocobase-password`, `NativeLab-2026!`).
- **NEVER** deploy loopback fixture passwords, keys, or certificates to Internet-facing or production environments. Rotate all secrets, TLS certificates, OIDC client secrets, and signing keys before any non-loopback deployment.

---

## 4. Reporting a Vulnerability / Quy trình báo cáo lỗ hổng bảo mật

- **Do NOT** disclose security vulnerabilities, tokens, `.env` files, private keys, or personal data in public GitHub Issues, Pull Requests, or Discussions.
- Report security issues privately via **GitHub Security Advisories**:
  - **https://github.com/CTU-OpenPaw/lattice-dx/security/advisories/new**
- Please include:
  1. Affected repository (`lattice-dx`, `lattice-docs`, `lattice-templates`, or `.github`) and version/commit SHA (`0.4.2`).
  2. Impact scope and component/endpoint involved.
  3. Minimal, reproducible steps or proof-of-concept.
- Maintainers acknowledge reports promptly and coordinate remediation and disclosure responsibly.
