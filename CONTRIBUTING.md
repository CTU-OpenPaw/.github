# Contributing to CTU-OpenPaw / Hướng dẫn Đóng góp (Pure-OSI Apache-2.0)

Thank you for contributing to the **CTU-OpenPaw** open-source ecosystem! These organization-wide guidelines apply to all four repositories:

| Repository | Purpose | License |
| --- | --- | --- |
| [`CTU-OpenPaw/lattice-dx`](https://github.com/CTU-OpenPaw/lattice-dx) | Core DX-OS Open-Core Enterprise Platform (`v0.4.2`) | `Apache-2.0` |
| [`CTU-OpenPaw/lattice-docs`](https://github.com/CTU-OpenPaw/lattice-docs) | Bilingual VitePress Documentation Portal (`VI` / `EN`) | `Apache-2.0` (VitePress `MIT`) |
| [`CTU-OpenPaw/lattice-templates`](https://github.com/CTU-OpenPaw/lattice-templates) | Sanitized LatticeFlow v2, DMN, n8n, Form & Business Packs | `Apache-2.0` |
| [`CTU-OpenPaw/.github`](https://github.com/CTU-OpenPaw/.github) | Organization Profile, Branding & OSS Governance | `Apache-2.0` |

---

## 1. Development Environment / Môi trường phát triển

- **Node.js 24** (`>=24 <25`)
- **pnpm 10.23.0** (`corepack enable` or `npm i -g pnpm@10.23.0`)
- **Git**, **tar**, and **Docker Compose v2+** (for container/stack validation)

```sh
git clone https://github.com/CTU-OpenPaw/lattice-dx.git
cd lattice-dx
pnpm install --frozen-lockfile
```

For full offline/source build instructions, see [`docs/build-from-source.md`](https://github.com/CTU-OpenPaw/lattice-dx/blob/main/docs/build-from-source.md) or the [live documentation portal](https://ctu-openpaw.github.io/lattice-docs/).

---

## 2. Version Lock & Pure-OSI Rules / Khóa phiên bản & Quy tắc Pure-OSI

### Strict Pre-Major Version Lock (`0.4.2`)
- All repositories follow a strict **`0.x` pre-major version lock** (`0.4.2`).
- **NEVER** bump version numbers, tags, docs, or manifests to a `1.x` major version without explicit maintainer approval.

### Pure-OSI Apache-2.0 & Zero-Upstream Policy
1. **SPDX Headers:** Every authored first-party source, script, workflow, or config file supporting comments (`.ts`, `.tsx`, `.js`, `.jsx`, `.cjs`, `.mjs`, `.yml`, `Dockerfile`) must carry:
   ```text
   @copyright (C) 2026 CTU-OpenPaw
   SPDX-License-Identifier: Apache-2.0
   ```
   Verified via `node scripts/source-headers.mjs --check` in `lattice-dx`.
2. **Zero `BSL-1.1` / Non-OSI Core Dependencies:** Never introduce `BSL-1.1` software (such as ThingsBoard or EMQX) or unverified non-OSI dependencies into source code, `package.json`, lockfiles, SBOMs, or core compose manifests.
3. **Zero Upstream NocoBase Source (`upstream/` / `.gitmodules`):**
   - Repositories must contain **ZERO** `upstream/nocobase/` directories and **ZERO** `.gitmodules` files.
   - Host `nocobase/nocobase:2.2.50` runs strictly as an external OCI container. First-party plugins declare `"nocobase": { "sdkProvidedByHost": true }`.
4. **NocoBase Clause 5.2 Attribution:** Preserve transparent NocoBase Clause 5.2 UI attribution (`"Powered by NocoBase"`, `"v2.2.50"`, `"https://www.nocobase.com"`, `"License settings"`) in platform UI, `NOTICE`, and documentation.
5. **Zero Secrets & Clean Naming:** Never commit `.env`, tokens, private keys (`*.pem`, `*.key`), production data, or deprecated legacy terms.

---

## 3. Code & Documentation Standards / Quy ước mã nguồn & Tài liệu

- **Modular Structure (`lattice-dx`):** Standalone services in `apps/`, shared contracts/packages in `packages/`, NocoBase host extensions in `plugins/nocobase/`. Keep new source files focused (**recommended `< 300` lines/file**).
- **Bilingual Parity (`lattice-docs` & READMEs):** User-facing documentation maintains **Vietnamese (`/` / `README.md`)** and **English (`/en/` / `README.en.md`)** parity with metrics grounded strictly in verified codebase facts (`0.4.2`).
- **Sanitized Templates (`lattice-templates`):** Every JSON blueprint, n8n workflow, DMN table, public form, or solution bundle must contain zero secrets, zero personal data (PII), and pass `scripts/validate-templates.mjs`.
- **Immutable Container Digests:** Any OCI container image referenced in `infra/compose/*.yml` must be pinned with an immutable `@sha256:` digest.

---

## 4. Commit & Pull Request Workflow / Quy trình Commit & Pull Request

Use **Conventional Commits** (`feat:`, `fix:`, `docs:`, `chore:`, `test:`, `refactor:`, `ci:`) and open PRs targeting `main`.

### Pre-PR Verification Checklist / Checklist bắt buộc trước khi gửi PR

#### Core Repository (`lattice-dx`)
1. `pnpm check` (`typecheck`, `vitest`, workspace `build`) passes cleanly.
2. `node scripts/source-headers.mjs --check` and `pnpm pof:check` (`6/6 PASS`) pass.
3. `pnpm release:sbom -- --check` (`257/257`, `0 NONE`, `0 BSL-1.1`) and `pnpm audit --prod --audit-level=low` (`0` vulnerabilities) pass.
4. `node --test plugins/nocobase/*/tests/*.test.cjs scripts/tests/*.test.mjs tests/*.test.mjs` and `node scripts/eval-vietnamese-nl-agent.mjs --min-pass=95` pass.
5. For `infra/compose/*.yml` edits, verify all images pin `@sha256:` and run `docker compose -f <file> config --quiet`.

#### Documentation Portal (`lattice-docs`)
1. `pnpm docs:build` completes with zero broken links and zero errors.
2. Both Vietnamese (`/`) and English (`/en/`) routes build and stay synchronized.

#### Templates Repository (`lattice-templates`)
1. `node scripts/validate-templates.mjs` passes (valid JSON, schema check, zero secrets, zero `BSL-1.1`, Apache-2.0 compliance).

---

## 5. Community & Security / Cộng đồng & Bảo mật

- All contributors agree to abide by our [Code of Conduct](CODE_OF_CONDUCT.md) (Contributor Covenant v2.1).
- Report security vulnerabilities privately via [GitHub Security Advisories](https://github.com/CTU-OpenPaw/lattice-dx/security/advisories/new) — see [SECURITY.md](SECURITY.md).
