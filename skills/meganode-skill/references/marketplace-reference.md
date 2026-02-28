# API Marketplace Reference

## Overview

NodeReal operates an API Marketplace offering both first-party enhanced APIs and third-party integrations. These extend MegaNode's core RPC functionality with specialized data services for NFTs, DeFi, identity, and more.

---

## First-Party APIs

### BNBBurn API

Provides access to BNB chain block rewards and burn information.

**Pricing:** Free / $99/mo

```json
{
  "method": "nr_getDailyBlockReward",
  "params": ["2024-01-01", "2024-01-31"]
}
```

### BNB Chain Staking API

Retrieve staking statistics from the official BNB Chain Staking Service.

```json
{
  "method": "nr_getStakingStats",
  "params": []
}
```

### opBNB Enhanced API

Enhanced `nr_` methods available on opBNB chain:

| Method | Description |
|--------|-------------|
| `nr_getTokenBalance20` | ERC-20 token balance on opBNB |
| `nr_getTokenHoldings` | Token holdings on opBNB |
| `nr_getNFTHoldings` | NFT holdings on opBNB |
| `nr_getAssetTransfers` | Transaction history on opBNB |

### COMBO Enhanced API

Enhanced API methods for the COMBO chain (BNB ecosystem L2).

---

## Third-Party Marketplace APIs

### NFTScan API

Comprehensive NFT data service with 40+ endpoints.

**NFT Asset Queries:**
- Get NFTs by account / Get all NFTs by account / Get minted NFTs by account
- Get NFTs by contract / Get single NFT / Get multiple NFTs
- Search NFTs / Get NFTs by attributes
- Get all multi-chain NFTs by account

**NFT Transaction Queries:**
- Get transactions by account / Get transactions by contract
- Get transactions by NFT / Search transactions
- Get transactions by to address / Get transactions by hash

**NFT Collection Queries:**
- Get an NFT collection / Search NFT collections
- Get NFT collections by account / Get NFT collections by ranking

**Rankings & Statistics:**
- Trade Ranking / Mint Ranking / Mint Amount / Traders Ranking
- Gas Ranking / Collection Ranking / Marketplace Ranking / Market Cap Ranking
- Collection Statistics / Collection Trade Distribution / Collection Trending Statistics
- Blue Chip

**Account & Collection Analytics:**
- Account Overview / Account Holding Distribution / Collection Overview

**Utilities:**
- Refresh NFT metadata / Refresh NFT metadata by contract
- Get latest block number / Get NFT amount by account
- Get NFT owners by contract / Get owners by an NFT

**Example:**
```javascript
// Get NFTs by account
const response = await fetch(
  `https://open-platform-ap.nodereal.io/${API_KEY}/nftscan/getAccountNfts`,
  {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      account_address: "0xWalletAddress",
      erc_type: "erc721",
      chain: "bsc",
    }),
  }
);
```

### Contracts API

Smart contract source code retrieval, ABI query, and verification services.

**Supported chains:** BSC Mainnet, BSC Testnet, opBNB Mainnet, opBNB Testnet

**Endpoint format:**
```
https://open-platform.nodereal.io/{API-key}/{chain-network}/contract/?action={action}&address={address}
```

**Chain network identifiers:**
- `bsc-mainnet` — BSC Mainnet
- `bsc-testnet` — BSC Testnet
- `op-bnb-mainnet` — opBNB Mainnet
- `op-bnb-testnet` — opBNB Testnet

#### Get Contract Source Code for Verified Contracts

Returns the Solidity source code of a verified smart contract.

```bash
# GET request
curl "https://open-platform.nodereal.io/${NODEREAL_API_KEY}/bsc-mainnet/contract/?action=getsourcecode&address=0x729dCFb5a7f55c145939e98D85Bf96aFE61Db0a1"
```

```javascript
const response = await fetch(
  `https://open-platform.nodereal.io/${API_KEY}/bsc-mainnet/contract/?action=getsourcecode&address=0x729dCFb5a7f55c145939e98D85Bf96aFE61Db0a1`
);
const data = await response.json();
// data.result[0].SourceCode — Solidity source code
// data.result[0].ContractName — Contract name
// data.result[0].CompilerVersion — Compiler version
// data.result[0].OptimizationUsed — Optimization flag
// data.result[0].ABI — Contract ABI
```

#### Get Contract ABI for Verified Contracts

Returns the ABI for a verified smart contract.

```bash
curl "https://open-platform.nodereal.io/${NODEREAL_API_KEY}/bsc-mainnet/contract/?action=getabi&address=0x729dCFb5a7f55c145939e98D85Bf96aFE61Db0a1"
```

```javascript
const response = await fetch(
  `https://open-platform.nodereal.io/${API_KEY}/bsc-mainnet/contract/?action=getabi&address=0x729dCFb5a7f55c145939e98D85Bf96aFE61Db0a1`
);
const data = await response.json();
const abi = JSON.parse(data.result); // Parse ABI JSON string
```

#### Get Contract Creation Transaction (JSON-RPC)

Returns deployer address and creation transaction hash. Uses the standard RPC endpoint.

```javascript
const response = await fetch(
  `https://bsc-mainnet.nodereal.io/v1/${API_KEY}`,
  {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      jsonrpc: "2.0",
      id: 1,
      method: "nr_getContractCreationTransaction",
      params: ["0x729dCFb5a7f55c145939e98D85Bf96aFE61Db0a1"],
    }),
  }
);
// CU Cost: 250
```

#### Verify Source Code

Submit contract source code for verification on BscScan/opBNBScan.

```bash
curl -X POST "https://open-platform.nodereal.io/${NODEREAL_API_KEY}/bsc-mainnet/contract/?action=verifysourcecode" \
  -H "Content-Type: application/json" \
  -d '{
    "address": "0xContractAddress",
    "sourceCode": "// SPDX-License-Identifier: MIT ...",
    "contractName": "MyContract",
    "compilerVersion": "v0.8.19+commit.7dd6d404",
    "optimizationUsed": 1,
    "runs": 200
  }'
```

#### Verify Proxy Contract

Verify proxy contract implementation for transparent/UUPS proxy patterns.

```bash
curl -X POST "https://open-platform.nodereal.io/${NODEREAL_API_KEY}/bsc-mainnet/contract/?action=verifyproxycontract" \
  -H "Content-Type: application/json" \
  -d '{"address": "0xProxyContractAddress"}'
```

#### Methods Summary

| Action | HTTP | Description | CU Cost |
|--------|------|-------------|---------|
| `getsourcecode` | GET | Get verified contract source code | — |
| `getabi` | GET | Get verified contract ABI | — |
| `nr_getContractCreationTransaction` | POST (RPC) | Get deployer and creation tx | 250 |
| `verifysourcecode` | POST | Submit source for verification | — |
| `verifyproxycontract` | POST | Verify proxy contract | — |

### Covalent API

Unified blockchain data across multiple chains.

**Key Features:**
- Token balances (all tokens for an address in one call)
- Transaction history with decoded events
- Cross-chain data aggregation

### SPACE ID Universal Name Service API

Universal name service for Web3 (ENS-like for BNB Chain).

**Methods:**
- Resolve name → address
- Reverse resolve address → name
- Domain availability check

**Example:**
```javascript
const result = await fetch(
  `https://open-platform-ap.nodereal.io/${API_KEY}/spaceid/resolve`,
  {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      name: "example.bnb",
    }),
  }
);
```

### PancakeSwap GraphQL v2

DEX data queries for PancakeSwap (Premium tier).

**Queries:**
- Pair data (liquidity, volume, price)
- Token statistics
- Transaction history
- Swap events

### Particle Bundler RPC

Account Abstraction (ERC-4337) bundler service.

**Methods:**
- `eth_sendUserOperation` — Submit user operation
- `eth_estimateUserOperationGas` — Gas estimation
- `eth_getUserOperationByHash` — Query operation status
- `eth_supportedEntryPoints` — Get supported entry points

---

## Blockchain RPC via Marketplace

The following blockchain RPCs are available through the Web3 API Marketplace:

### Arbitrum Nova

Standard EVM JSON-RPC for Arbitrum Nova (AnyTrust chain).

```
https://arb-nova-mainnet.nodereal.io/v1/{API-key}
```

### Arbitrum Nitro

Standard EVM JSON-RPC for Arbitrum One (Nitro rollup).

```
https://arb-mainnet.nodereal.io/v1/{API-key}
```

### Avalanche Contract Chain (C-Chain)

Standard EVM JSON-RPC for Avalanche C-Chain.

```
https://avalanche-mainnet.nodereal.io/v1/{API-key}
```

### Klaytn RPC

Standard EVM JSON-RPC for Klaytn network.

```
https://klaytn-mainnet.nodereal.io/v1/{API-key}
```

### zkSync RPC

zkSync Era supports two API categories:

**Ethereum JSON-RPC API** — Standard EVM JSON-RPC methods (eth_*, net_*, web3_*)
```
https://zksync-mainnet.nodereal.io/v1/{API-key}
```

**zkSync JSON-RPC API** — zkSync-specific methods (zks_*)
```
https://zksync-mainnet.nodereal.io/v1/{API-key}
```

### BASE RPC

Standard EVM JSON-RPC for BASE (Coinbase L2).

```
https://base-mainnet.nodereal.io/v1/{API-key}
```

### COMBO RPC

Standard EVM JSON-RPC for COMBO chain (BNB ecosystem L2).

```
https://combo-mainnet.nodereal.io/v1/{API-key}
```

### Greenfield Enhanced API

Enhanced API methods for BNB Greenfield decentralized storage network.

**Methods:**
- Get transaction list by address
- Get policy list by resource ID
- Get daily bucket list
- Get transaction list
- Get daily transaction list
- Get daily account list
- Get daily object list
- Get daily total quota list

### Greenfield Billing API

Billing and quota tracking for BNB Greenfield storage operations.

**Methods:**
- Get total cost by owner address
- Get total cost by payment address
- Get monthly bill by owner address
- Get monthly bill by payment address
- Get real time bill by owner address
- Get real time bill count by owner address
- Get real time bill by payment address
- Get real time bill count by payment address

---

## Accessing Marketplace APIs

All marketplace APIs are accessed through the open platform endpoint:

```
https://open-platform-ap.nodereal.io/{API-key}/{service-name}/{method}
```

**Activation:** Marketplace APIs must be activated in the MegaNode dashboard before use. Some require additional subscription fees.

---

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| `getsourcecode` returns empty | Contract is not verified | Only verified contracts return source code; verify first |
| Marketplace API returns 403 | API not activated in dashboard | Activate the API in MegaNode dashboard under Marketplace |
| NFTScan returns empty results | Wrong chain or address format | Verify chain parameter and address checksum |
| Contracts API wrong chain | Using incorrect chain-network identifier | Use `bsc-mainnet`, `bsc-testnet`, `opbnb-mainnet`, or `opbnb-testnet` |
| Greenfield API errors | API requires specific activation | Activate Greenfield APIs separately in the dashboard |
| `verifysourcecode` fails | Compiler version mismatch or wrong params | Match exact compiler version and optimization settings from deployment |

## Documentation

- **NodeReal Marketplace:** https://nodereal.io/marketplace
- **Contracts API:** https://docs.nodereal.io/reference/get-contract-source-code-for-verified-contract-source-codes
- **NFTScan API:** https://docs.nodereal.io/reference/nftscan-api
- **API Reference:** https://docs.nodereal.io/reference
