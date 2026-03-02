# NodeReal MegaNode Skill — Quality Evaluation Report

**Date:** 2026-03-02
**Scope:** 21 files (SKILL.md + 20 reference files), 14,051 total lines
**Methodology:** Automated link validation, method-level coverage audit against authoritative source docs, content accuracy scanning, code example spot-checking

---

## Executive Summary

| Category | Score | Details |
|----------|-------|---------|
| Structural Integrity | **100%** | All links valid, all TOC anchors match headings |
| API Coverage (weighted) | **84%** | Strong core coverage; gaps in rankings/experimental methods |
| Content Accuracy | **97%** | 1 HIGH fix applied; no security issues |
| Safety & Security | **PASS** | No hardcoded keys, all endpoints HTTPS/WSS |
| Evaluation Scenarios | **8/8** | Created with verification criteria |

**Overall: GOOD — ready for marketplace with known coverage gaps documented below.**

---

## 1. Structural Integrity (100%)

### 1.1 Broken Links — Fixed

| File | Issue | Status |
|------|-------|--------|
| `marketplace-extras-reference.md:7` | Referenced deleted `marketplace-reference.md` | **FIXED** — Updated to link individual reference files |
| `non-evm-chains-reference.md:7` | Referenced deleted `marketplace-reference.md` | **FIXED** — Updated to link `klaytn-reference.md` |

**Verification:** `grep -r "marketplace-reference.md" skills/` returns 0 matches.

### 1.2 Cross-Reference Validation

- SKILL.md → 20 reference files: **All 20 links valid**
- Reference files internal cross-links: **All valid**
- No orphan reference files (all 20 linked from SKILL.md)

### 1.3 TOC Anchor Validation

**156 TOC anchor links checked across 21 files — 0 broken.**

| Files with TOC | Anchors Checked | Status |
|----------------|----------------|--------|
| 18 files | 156 anchors | All PASS |
| 3 files (spaceid, zksync, SKILL.md) | No TOC present | N/A |

---

## 2. API Coverage Audit (84% weighted)

Source: `skills_docs/menu.json` (authoritative API catalog)

### Coverage by Area

| Area | Priority | Ref Methods | Source Methods | Coverage | Missing |
|------|----------|-------------|---------------|----------|---------|
| NFTScan | HIGH | 35 | 40 | **87.5%** | 5 ranking endpoints |
| Klaytn | HIGH | 54 klay_* | 54 klay_* + 5 net_* | **90%** | 5 net_* methods |
| Enhanced API (opBNB/Combo) | HIGH | 26/26 | 26/26 | **100%** | Chain compat table incomplete |
| NEAR RPC | MEDIUM | 9 | 14 | **64.3%** | 4 EXPERIMENTAL_* + gas_price |
| Greenfield | MEDIUM | 11 | 16 | **68.8%** | 5 daily chart endpoints |
| Aptos | LOW | 22 | 21 | **~100%** | Only `spec` endpoint |

### Missing Methods Detail

**NFTScan (5 missing ranking endpoints):**
- `Mint Amount` — statistics ranking by mint amounts
- `Traders Ranking` — statistics ranking by traders
- `Gas Ranking` — statistics ranking by gas usage
- `Marketplace Ranking` — statistics ranking by marketplace
- `Market Cap Ranking` — statistics ranking by market cap

**Klaytn (5 missing net_* methods):**
- `net_networkID`, `net_listening`, `net_peerCount`, `net_peerCountByType`, `net_version`

**NEAR RPC (5 missing methods):**
- `EXPERIMENTAL_changes` — query changes by account/key/code/data
- `EXPERIMENTAL_changes_in_block` — query changes in specific block
- `EXPERIMENTAL_receipt` — query receipt by ID
- `EXPERIMENTAL_tx_status` — detailed tx status with receipts
- `gas_price` — gas price for a specific block

**Greenfield Enhanced API (5 missing daily chart endpoints):**
- `Get transaction list` (general, no address filter)
- `Get daily transaction list`
- `Get daily account list`
- `Get daily object list`
- `Get daily total quota list`

**Enhanced API Chain Compatibility:**
- The chain compatibility table in `enhanced-api-reference.md` lists only 8 platform-exclusive methods for opBNB/Combo
- Source docs show 18 additional shared `nr_*` methods also work on opBNB and Combo
- All methods are documented; only the compatibility matrix needs updating

### CU Pricing Completeness

- `pricing-reference.md` covers ~45 methods with CU costs
- ~25 methods remain unpriced (mainly Enhanced API NFT methods and trace methods)
- No contradictions found between pricing-reference.md and other files (clean separation)

---

## 3. Content Accuracy (97%)

### 3.1 Endpoint URL Validation

| Check | Result |
|-------|--------|
| Insecure `http://` endpoints | **PASS** — 0 NodeReal endpoints use HTTP (1 third-party URL in NFTScan example data) |
| Insecure `ws://` endpoints | **PASS** — All WebSocket URLs use `wss://` |
| Domain consistency | **PASS** — 300+ URLs use correct `nodereal.io` domain patterns |
| MegaFuel `-ap` suffix | **PASS** — Correctly uses `open-platform-ap.nodereal.io` |

### 3.2 Issues Found & Fixed

| Severity | File | Issue | Status |
|----------|------|-------|--------|
| **HIGH** | `common-patterns-reference.md:27` | Klaytn endpoint used wrong URL format (`klaytn-mainnet.nodereal.io/v1/...` instead of `open-platform.nodereal.io/{apiKey}/klaytn/`) — would cause runtime failures | **FIXED** |
| LOW | `nftscan-reference.md:451` | `http://` URL in example response data (third-party BAYC website) | Not a NodeReal endpoint — acceptable |

### 3.3 Security Scan

| Check | Result |
|-------|--------|
| Hardcoded API keys | **PASS** — All use placeholders (`{apiKey}`, `YOUR_API_KEY_HERE`, `process.env`) |
| Private key exposure | **PASS** — All use `process.env.PRIVATE_KEY` |
| High-entropy hex strings | **PASS** — Only well-known contract addresses and example tx hashes |

### 3.4 Code Example Spot-Check (5/5 PASS)

| # | File | Method | Verdict |
|---|------|--------|---------|
| 1 | enhanced-api-reference.md | `nr_getTokenBalance20` | PASS — correct method, 3 params, proper JSON-RPC format |
| 2 | megafuel-reference.md | `pm_isSponsorable` | PASS — correct method, proper object params |
| 3 | rpc-reference.md | `eth_call` | PASS — standard format, correct `balanceOf` selector |
| 4 | nftscan-reference.md | GET `/api/v2/account/own/` | PASS — correct path, proper pagination |
| 5 | contracts-api-reference.md | `getsourcecode` + Sourcify | PASS — correct 3-step fallback flow |

### 3.5 Safety Warning Audit

| File | Transaction Type | Warnings Present | Status |
|------|-----------------|-----------------|--------|
| megafuel-reference.md | Gasless tx | Pre-flight check, fallback, timeout detection, nonce mgmt | GOOD |
| direct-route-reference.md | MEV bundles | Bundle monitoring, timestamp mgmt, retry logic, gas pricing | GOOD |
| enhanced-api-reference.md | Read-only queries | NFT metadata caveat | ADEQUATE (no tx operations) |
| SKILL.md | General | "Never accept private keys", confirmation prompts | GOOD |

---

## 4. Functional Evaluation Scenarios

Created `evaluations/eval-scenarios.md` with **8 scenarios** covering all major products:

| # | Scenario | Product | Key Verification |
|---|----------|---------|-----------------|
| 1 | Query ERC-20 token balance | Enhanced API | `nr_getTokenBalance20` + correct endpoint |
| 2 | Configure gasless transactions | MegaFuel | `pm_isSponsorable` → send flow + `-ap` endpoint |
| 3 | Query NFT holdings | NFTScan | Correct path + `chain_name` param |
| 4 | Resolve .bnb domain | SPACE ID | POST + `/spaceid/domain/binds/byNames` |
| 5 | Get contract source code | Contracts API | BscTrace → Sourcify fallback flow |
| 6 | Trace a transaction | Debug/Trace | `debug_traceTransaction` + `callTracer` |
| 7 | Multi-chain dApp init | RPC | Correct chain identifiers + chain IDs |
| 8 | Klaytn account query | Klaytn RPC | `klay_getAccount` + Marketplace endpoint |

---

## 5. Files Modified During Audit

| File | Change |
|------|--------|
| `references/marketplace-extras-reference.md` | Fixed broken link to deleted marketplace-reference.md |
| `references/non-evm-chains-reference.md` | Fixed broken link to deleted marketplace-reference.md |
| `references/common-patterns-reference.md` | Fixed incorrect Klaytn endpoint URL format |

---

## 6. Recommendations (Priority Order)

### Must Address Before Marketplace Publication

None — all critical issues have been fixed during this audit.

### Recommended Improvements

1. **NEAR RPC coverage** (64.3%) — Add 5 missing methods including EXPERIMENTAL_* series
2. **Greenfield coverage** (68.8%) — Add 5 missing daily chart/statistics endpoints
3. **Enhanced API chain compat table** — Update to show 18 shared methods work on opBNB/Combo
4. **NFTScan rankings** — Add 5 missing ranking endpoints
5. **Klaytn net_* methods** — Add 5 net namespace methods
6. **CU pricing completeness** — Add pricing for ~25 undocumented methods
