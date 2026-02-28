# Enhanced API Reference

## Overview

NodeReal Enhanced APIs extend standard JSON-RPC with proprietary methods prefixed with `nr_` and `ng_`. These provide rich token, NFT, and transaction data without the need for custom indexing infrastructure.

The Enhanced API is organized into these sub-categories (matching the NodeReal API Reference sidebar):

- **Fungible Tokens API** — ERC-20 token balances, metadata, holdings, holders
- **NFT API** — ERC-721/ERC-1155 holdings, holders, inventory
- **Platform API** — Account lists, account counts (`nr_getAccountList`, `nr_getAccountListCount`)
- **Transaction Pool API** — `txpool_content`, `txpool_status`, `txpool_inspect`
- **Transaction Receipts API** — `nr_getAssetTransfers`, `nr_getAssetTransfersCount`, `nr_getContractCreationTransaction`, `ng_getTransactionByAddress`
- **opBNB Enhanced API** — Enhanced methods available on opBNB (same `nr_` methods, opBNB chain)
- **BNBBurn API** — `nr_getDailyBlockReward`, `nr_getDailyBlockCountAndReward`
- **Combo Enhanced API** — Enhanced methods for COMBO chain (BNB ecosystem L2)

## Fungible Tokens API (ERC-20)

### `nr_getTokenBalance20`
Get ERC-20 token balance for an address.

**CU Cost:** 25

```json
{
  "method": "nr_getTokenBalance20",
  "params": [
    "0xTokenContract",  // ERC-20 contract address
    "0xWalletAddress",  // Holder address
    "latest"           // Block tag
  ]
}
```

**Response:**
```json
{
  "result": "0x56bc75e2d63100000"  // Balance in hex (wei)
}
```

### `nr_getTotalSupply20`
Get total supply of an ERC-20 token.

**CU Cost:** 25

```json
{
  "method": "nr_getTotalSupply20",
  "params": [
    "0xTokenContract",  // ERC-20 contract address
    "latest"
  ]
}
```

### `nr_getTokenMeta`
Get token metadata (name, symbol, decimals).

**CU Cost:** 25

```json
{
  "method": "nr_getTokenMeta",
  "params": ["0xTokenContract"]
}
```

**Response:**
```json
{
  "result": {
    "name": "USD Coin",
    "symbol": "USDC",
    "decimals": 6
  }
}
```

### `nr_getTokenHoldings`
Get all ERC-20 tokens held by an account.

**CU Cost:** 25

```json
{
  "method": "nr_getTokenHoldings",
  "params": [
    "0xWalletAddress",  // Account address
    "0x1",             // Page number (hex)
    "0x14"             // Page size (hex, max 50)
  ]
}
```

**Response:**
```json
{
  "result": {
    "tokenList": [
      {
        "tokenAddress": "0x...",
        "tokenName": "USD Coin",
        "tokenSymbol": "USDC",
        "tokenDecimals": 6,
        "balance": "1000000000"
      }
    ],
    "totalCount": 15
  }
}
```

### `nr_getTokenCount`
Get count of distinct ERC-20 tokens held by an address.

**CU Cost:** 25

```json
{
  "method": "nr_getTokenCount",
  "params": ["0xWalletAddress"]
}
```

### `nr_getTokenHolders`
Get list of token holders with balances.

**CU Cost:** 100

```json
{
  "method": "nr_getTokenHolders",
  "params": [
    "0xTokenContract",  // Token contract address
    "0x1",             // Page number
    "0x14"             // Page size
  ]
}
```

### `nr_getTokenHoldingCount`
Get count of distinct ERC-20 tokens held by an address.

**CU Cost:** 25

```json
{
  "method": "nr_getTokenHoldingCount",
  "params": ["0xWalletAddress"]
}
```

### `nr_historyTokenHolderSend`
Get historical token holder send records.

**CU Cost:** 50

```json
{
  "method": "nr_historyTokenHolderSend",
  "params": ["0xTokenContract", "0xBlockNumber", "0x1", "0x14"]
}
```

### `nr_getHistoryTokenHolder`
Get historical token holder information at a specific block.

**CU Cost:** 50

```json
{
  "method": "nr_getHistoryTokenHolder",
  "params": ["0xTokenContract", "0xBlockNumber", "0x1", "0x14"]
}
```

### `nr_getTokenHolderCount`
Get count of token holders.

**CU Cost:** 50

> **Note:** Not real-time — may have hours of latency.

```json
{
  "method": "nr_getTokenHolderCount",
  "params": ["0xTokenContract"]
}
```

---

## NFT APIs (ERC-721 / ERC-1155)

### `nr_getNFTHoldings`
Get NFT holdings for an address.

**CU Cost:** 25

```json
{
  "method": "nr_getNFTHoldings",
  "params": [
    "0xWalletAddress",     // Holder address
    "0xContractAddress",   // NFT contract (empty string for all)
    "0x1",                // Token type: 0x1=ERC721, 0x2=ERC1155
    "0x1",                // Page number
    "0x14"                // Page size
  ]
}
```

**Response:**
```json
{
  "result": {
    "details": [
      {
        "tokenAddress": "0x...",
        "tokenId": "1234",
        "balance": "1",
        "tokenName": "My NFT Collection",
        "tokenSymbol": "MNC"
      }
    ],
    "totalCount": 42
  }
}
```

### `nr_getNFTInventory`
Get count of ERC-721 token IDs per collection for an address.

**CU Cost:** 25

```json
{
  "method": "nr_getNFTInventory",
  "params": [
    "0xWalletAddress",
    "0xContractAddress",
    "0x1",   // Page number
    "0x14"   // Page size
  ]
}
```

### `nr_getNFTHolders`
Get owners for a specific ERC-721/ERC-1155 tokenId.

**CU Cost:** 100

```json
{
  "method": "nr_getNFTHolders",
  "params": [
    "0xContractAddress",  // NFT contract
    "0x1",               // Token ID (hex)
    "0x1",               // Page number
    "0x14"               // Page size
  ]
}
```

### `nr_getNFTHoldersWithBalance`
Get owners with balance for a specific tokenId.

**CU Cost:** 100

```json
{
  "method": "nr_getNFTHoldersWithBalance",
  "params": [
    "0xContractAddress",
    "0x1",   // Token ID
    "0x1",   // Page number
    "0x14"   // Page size
  ]
}
```

### `nr_getNFTHolderCount`
Get total number of NFT holders for a contract.

**CU Cost:** 50

```json
{
  "method": "nr_getNFTHolderCount",
  "params": ["0xContractAddress", "0x1"]  // contract, tokenType
}
```

### `nr_getNFTHoldingCount`
Get NFT holding count for an address.

**CU Cost:** 25

```json
{
  "method": "nr_getNFTHoldingCount",
  "params": [
    "0xWalletAddress",
    "0xContractAddress",
    "0x1"  // Token type
  ]
}
```

### `nr_getNFTCollectionHolderCount`
Get holder count for an entire NFT collection.

**CU Cost:** 50

```json
{
  "method": "nr_getNFTCollectionHolderCount",
  "params": ["0xContractAddress"]
}
```

### `nr_getNFTCollectionHolders`
Get list of holders for an entire NFT collection.

**CU Cost:** 100

```json
{
  "method": "nr_getNFTCollectionHolders",
  "params": ["0xContractAddress", "0x1", "0x14"]
}
```

### `nr_getNFTMeta`
Get metadata for a specific NFT token (name, description, image, attributes).

**CU Cost:** 25

```json
{
  "method": "nr_getNFTMeta",
  "params": ["0xContractAddress", "0x1"]
}
```

### `nr_getNFTTokenCount`
Get count of token IDs in an NFT collection.

**CU Cost:** 25

```json
{
  "method": "nr_getNFTTokenCount",
  "params": ["0xContractAddress"]
}
```

### `nr_getNFTTokens`
Get list of token IDs in an NFT collection.

**CU Cost:** 50

```json
{
  "method": "nr_getNFTTokens",
  "params": ["0xContractAddress", "0x1", "0x14"]
}
```

### `nr_getNFTTokenOwners`
Get all owners of a specific NFT token across collections.

**CU Cost:** 100

```json
{
  "method": "nr_getNFTTokenOwners",
  "params": ["0xContractAddress", "0x1", "0x1", "0x14"]
}
```

### `nr_getSummedSupply1155`
Get the summed supply of an ERC-1155 token across all token IDs.

**CU Cost:** 25

```json
{
  "method": "nr_getSummedSupply1155",
  "params": ["0xContractAddress"]
}
```

### `nr_getTokenBalance1155`
Get ERC-1155 token balance for a specific token ID.

**CU Cost:** 25

```json
{
  "method": "nr_getTokenBalance1155",
  "params": ["0xContractAddress", "0xTokenId", "0xWalletAddress", "latest"]
}
```

### `nr_getTokenBalance721`
Get ERC-721 token balance (count of owned tokens).

**CU Cost:** 25

```json
{
  "method": "nr_getTokenBalance721",
  "params": ["0xContractAddress", "0xWalletAddress", "latest"]
}
```

### `nr_getTotalSupply1155`
Get total supply of a specific ERC-1155 token ID.

**CU Cost:** 25

```json
{
  "method": "nr_getTotalSupply1155",
  "params": ["0xContractAddress", "0xTokenId", "latest"]
}
```

### `nr_getTotalSupply721`
Get total supply of an ERC-721 token.

**CU Cost:** 25

```json
{
  "method": "nr_getTotalSupply721",
  "params": ["0xContractAddress", "latest"]
}
```

---

## Platform API

### `nr_health`
Health check endpoint for the NodeReal platform.

**CU Cost:** 5

```json
{
  "method": "nr_health",
  "params": []
}
```

---

## Transaction & Account APIs (Transaction Receipts API)

### `nr_getAssetTransfers`
Get transaction history including normal, ERC-20, ERC-721, ERC-1155, and internal transactions.

**CU Cost:** 50

```json
{
  "method": "nr_getAssetTransfers",
  "params": [{
    "fromBlock": "0x0",
    "toBlock": "latest",
    "fromAddress": "0xWalletAddress",
    "category": ["external", "erc20", "erc721", "erc1155", "internal"],
    "maxCount": "0x14",
    "order": "desc"
  }]
}
```

**Categories:**
- `external` — Normal ETH/BNB transfers
- `erc20` — ERC-20 token transfers
- `erc721` — ERC-721 NFT transfers
- `erc1155` — ERC-1155 NFT transfers
- `internal` — Internal contract calls

### `nr_getAssetTransfersCount`
Get count of asset transfers matching criteria.

**CU Cost:** 25

```json
{
  "method": "nr_getAssetTransfersCount",
  "params": [{
    "fromAddress": "0xWalletAddress",
    "category": ["external", "erc20"]
  }]
}
```

### `nr_getContractCreationTransaction`
Get contract deployer address and creation transaction hash.

**CU Cost:** 25

```json
{
  "method": "nr_getContractCreationTransaction",
  "params": ["0xContractAddress"]
}
```

### `ng_getTransactionByAddress`
Get transactions by address.

**CU Cost:** 50

```json
{
  "method": "ng_getTransactionByAddress",
  "params": [
    "0xWalletAddress",
    "0x0",       // From block
    "latest",    // To block
    "0x1",       // Page number
    "0x14"       // Page size
  ]
}
```

### `nr_getTransactionDetail`
Get detailed transaction information including internal transactions.

**CU Cost:** 50

```json
{
  "method": "nr_getTransactionDetail",
  "params": ["0xTransactionHash"]
}
```

### `nr_getTransactionByAddress`
Get transactions sent from or to an address.

**CU Cost:** 50

```json
{
  "method": "nr_getTransactionByAddress",
  "params": ["0xWalletAddress", "0x0", "latest", "0x1", "0x14"]
}
```

### `nr_getTransactionByAddressCount`
Get count of transactions for an address.

**CU Cost:** 25

```json
{
  "method": "nr_getTransactionByAddressCount",
  "params": ["0xWalletAddress"]
}
```

### `nr_getTransactionReceiptsByBlockHash`
Get all transaction receipts in a block by block hash.

**CU Cost:** 50

```json
{
  "method": "nr_getTransactionReceiptsByBlockHash",
  "params": ["0xBlockHash"]
}
```

### `nr_getTransactionReceiptsByBlockNumber`
Get all transaction receipts in a block by block number.

**CU Cost:** 50

```json
{
  "method": "nr_getTransactionReceiptsByBlockNumber",
  "params": ["0xBlockNumber"]
}
```

### `nr_getAccountList`
Get active accounts ordered by token balance.

**CU Cost:** 100

```json
{
  "method": "nr_getAccountList",
  "params": [
    "0x1",   // Page number
    "0x14"   // Page size
  ]
}
```

---

## Block Statistics APIs

### `nr_getBlockNumberByTimeStamp`
Map a Unix timestamp to a block number.

**CU Cost:** 25

```json
{
  "method": "nr_getBlockNumberByTimeStamp",
  "params": [
    "1672531200",  // Unix timestamp
    "before"       // "before" or "after"
  ]
}
```

### `nr_getDailyBlockCountAndReward`
Get daily block count and rewards.

**CU Cost:** 50

```json
{
  "method": "nr_getDailyBlockCountAndReward",
  "params": [
    "2024-01-01",  // Start date
    "2024-01-31"   // End date
  ]
}
```

### `nr_getDailyBlockReward`
Get daily block reward distribution.

**CU Cost:** 50

```json
{
  "method": "nr_getDailyBlockReward",
  "params": [
    "2024-01-01",
    "2024-01-31"
  ]
}
```

---

## Supported Chains for Enhanced APIs

Enhanced APIs (`nr_`/`ng_` methods) are primarily available on:

| Chain | Token APIs | NFT APIs | Transfer APIs |
|-------|-----------|----------|---------------|
| BNB Smart Chain | Yes | Yes | Yes |
| Ethereum | Yes | Yes | Yes |
| Optimism | Partial | Partial | Partial |
| opBNB | Partial | Partial | Partial |

> Check the [NodeReal documentation](https://docs.nodereal.io/reference) for the latest chain-specific method availability.

## Pagination

All paginated methods use hex-encoded page numbers and page sizes:
- Page numbers start at `0x1`
- Page sizes are typically capped at `0x32` (50) or `0x64` (100)
- Use `totalCount` in the response to determine total pages

---

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Empty `tokenList` response | No tokens held or wrong address format | Verify address is checksummed; try without checksum |
| `totalCount` is 0 for NFT queries | Wrong token type parameter | Use `0x1` for ERC-721, `0x2` for ERC-1155 |
| Stale `nr_getTokenHolderCount` data | This method has hours of latency | Use for approximate counts; not for real-time data |
| Hex-encoded pagination confusion | Page numbers and sizes must be hex | Convert decimal to hex: page 1 = `0x1`, size 20 = `0x14` |
| `method not found` for `nr_` methods | Enhanced APIs not available on this chain | Check Supported Chains table above; primarily BSC and Ethereum |
| Large `nr_getAssetTransfers` response | Too many transactions in range | Use `fromBlock`/`toBlock` to narrow range; reduce `maxCount` |

## Documentation

- **Enhanced API Reference:** https://docs.nodereal.io/reference/nr_gettokenbalance20
- **API Reference (all methods):** https://docs.nodereal.io/reference
- **LLM-Optimized Docs:** https://docs.nodereal.io/llms.txt
