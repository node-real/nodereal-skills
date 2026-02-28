# MegaNode RPC API Reference

## Overview

MegaNode provides standard Ethereum-compatible JSON-RPC 2.0 endpoints over HTTPS and WSS for 25+ blockchain networks. All methods follow the standard JSON-RPC request/response format.

The standard RPC methods are available across **BNB Smart Chain**, **Ethereum**, and **Optimism** with identical interfaces. Each chain exposes the same JSON-RPC categories:

- **Account Information** — `eth_getBalance`, `eth_getCode`, `eth_getStorageAt`, `eth_getTransactionCount`
- **Chain Information** — `eth_chainId`, `net_version`, `net_listening`, `net_peerCount`, `web3_clientVersion`
- **Gas** — `eth_gasPrice`, `eth_estimateGas`, `eth_maxPriorityFeePerGas`, `eth_feeHistory`
- **Blocks** — `eth_blockNumber`, `eth_getBlockByHash`, `eth_getBlockByNumber`
- **Event Logs** — `eth_getLogs`, `eth_newFilter`, `eth_getFilterLogs`, `eth_getFilterChanges`
- **EVM** — `eth_call`, `eth_sendRawTransaction`
- **Transactions** — `eth_getTransactionByHash`, `eth_getTransactionReceipt`, `eth_getTransactionCount`
- **Uncle Blocks** — `eth_getUncleByBlockHashAndIndex`, `eth_getUncleByBlockNumberAndIndex`, `eth_getUncleCountByBlockHash`, `eth_getUncleCountByBlockNumber`
- **WebSockets** — `eth_subscribe` (newHeads, logs, newPendingTransactions, syncing)

## Endpoint Format

```
HTTPS: https://{chain}-{network}.nodereal.io/v1/{API-key}
WSS:   wss://{chain}-{network}.nodereal.io/ws/v1/{API-key}
```

## Request Format

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "method_name",
  "params": [...]
}
```

## Complete Method Reference

### Account Methods

| Method | CU Cost | Parameters | Description |
|--------|---------|------------|-------------|
| `eth_accounts` | 5 | none | Returns list of addresses owned by client |
| `eth_getBalance` | 15 | `[address, blockTag]` | Returns balance of the account |
| `eth_getCode` | 15 | `[address, blockTag]` | Returns code at a given address |
| `eth_getStorageAt` | 15 | `[address, position, blockTag]` | Returns value from storage position |
| `eth_getTransactionCount` | 25 | `[address, blockTag]` | Returns number of transactions sent |

### Block Methods

| Method | CU Cost | Parameters | Description |
|--------|---------|------------|-------------|
| `eth_blockNumber` | 5 | none | Returns latest block number |
| `eth_getBlockByHash` | 18 | `[hash, fullTx]` | Returns block by hash |
| `eth_getBlockByNumber` | 15 | `[blockTag, fullTx]` | Returns block by number |
| `eth_getBlockTransactionCountByHash` | 18 | `[hash]` | Returns tx count in block by hash |
| `eth_getBlockTransactionCountByNumber` | 18 | `[blockTag]` | Returns tx count in block by number |
| `eth_getUncleByBlockHashAndIndex` | 18 | `[hash, index]` | Returns uncle by block hash and index |
| `eth_getUncleByBlockNumberAndIndex` | 18 | `[blockTag, index]` | Returns uncle by block number and index |
| `eth_getUncleCountByBlockHash` | 18 | `[hash]` | Returns uncle count in block by hash |
| `eth_getUncleCountByBlockNumber` | 18 | `[blockTag]` | Returns uncle count in block by number |

### Transaction Methods

| Method | CU Cost | Parameters | Description |
|--------|---------|------------|-------------|
| `eth_getTransactionByHash` | 15 | `[hash]` | Returns transaction by hash |
| `eth_getTransactionByBlockHashAndIndex` | 15 | `[blockHash, index]` | Returns tx by block hash and index |
| `eth_getTransactionByBlockNumberAndIndex` | 15 | `[blockTag, index]` | Returns tx by block number and index |
| `eth_getTransactionReceipt` | 15 | `[hash]` | Returns transaction receipt |
| `eth_sendRawTransaction` | 150 | `[signedTxData]` | Submits a signed transaction |

### Call & Estimate Methods

| Method | CU Cost | Parameters | Description |
|--------|---------|------------|-------------|
| `eth_call` | 20 | `[txObject, blockTag]` | Executes a read-only call |
| `eth_estimateGas` | 75 | `[txObject]` | Estimates gas needed for transaction |
| `eth_gasPrice` | 15 | none | Returns current gas price |
| `eth_maxPriorityFeePerGas` | 15 | none | Returns max priority fee suggestion |
| `eth_feeHistory` | 15 | `[blockCount, newestBlock, rewardPercentiles]` | Returns fee history data |

### Filter Methods

| Method | CU Cost | Parameters | Description |
|--------|---------|------------|-------------|
| `eth_newFilter` | 18 | `[filterObject]` | Creates a new filter |
| `eth_newBlockFilter` | 18 | none | Creates a new block filter |
| `eth_newPendingTransactionFilter` | 18 | none | Creates pending tx filter |
| `eth_getFilterChanges` | 18 | `[filterId]` | Returns filter changes since last poll |
| `eth_getFilterLogs` | 50 | `[filterId]` | Returns all logs matching filter |
| `eth_getLogs` | 50 | `[filterObject]` | Returns logs matching filter criteria |
| `eth_uninstallFilter` | 10 | `[filterId]` | Removes a filter |

### Chain & Network Methods

| Method | CU Cost | Parameters | Description |
|--------|---------|------------|-------------|
| `eth_chainId` | 5 | none | Returns chain ID |
| `eth_syncing` | 5 | none | Returns sync status |
| `net_version` | 5 | none | Returns network ID |
| `net_listening` | 5 | none | Returns true if listening |
| `net_peerCount` | 10 | none | Returns number of peers |
| `web3_clientVersion` | 5 | none | Returns client version |
| `web3_sha3` | 10 | `[data]` | Returns Keccak-256 hash |

## Block Tags

Supported block tag values:
- `"latest"` — Most recent mined block
- `"earliest"` — Genesis block
- `"pending"` — Pending state/transactions
- `"safe"` — Latest safe block
- `"finalized"` — Latest finalized block
- Hex block number (e.g., `"0x1b4"`)

## Batch Requests

Send up to **500 requests** in a single batch:

```json
[
  {"jsonrpc":"2.0","id":1,"method":"eth_blockNumber","params":[]},
  {"jsonrpc":"2.0","id":2,"method":"eth_gasPrice","params":[]},
  {"jsonrpc":"2.0","id":3,"method":"eth_getBalance","params":["0x...", "latest"]}
]
```

## Error Codes

| Code | Message | Description |
|------|---------|-------------|
| `-32700` | Parse error | Invalid JSON |
| `-32600` | Invalid request | Invalid request object |
| `-32601` | Method not found | Method does not exist |
| `-32602` | Invalid params | Invalid method parameters |
| `-32603` | Internal error | Internal JSON-RPC error |
| `-32005` | Rate limit / CU exceeded | CUPS or monthly CU quota exceeded |

## Code Examples

### ethers.js v6

```javascript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider(
  `https://bsc-mainnet.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`
);

// Read operations
const blockNumber = await provider.getBlockNumber();
const balance = await provider.getBalance("0x...");
const block = await provider.getBlock(blockNumber);

// Contract interaction
const contract = new ethers.Contract(contractAddress, abi, provider);
const result = await contract.someReadMethod();

// Transaction submission (requires signer)
const wallet = new ethers.Wallet(process.env.PRIVATE_KEY, provider);
const tx = await wallet.sendTransaction({
  to: "0x...",
  value: ethers.parseEther("0.01"),
});
```

### viem

```javascript
import { createPublicClient, http } from "viem";
import { bsc } from "viem/chains";

const client = createPublicClient({
  chain: bsc,
  transport: http(`https://bsc-mainnet.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`),
});

const blockNumber = await client.getBlockNumber();
const balance = await client.getBalance({ address: "0x..." });
const block = await client.getBlock();
```

### Raw fetch

```javascript
async function rpcCall(method, params = []) {
  const response = await fetch(
    `https://bsc-mainnet.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`,
    {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        jsonrpc: "2.0",
        id: 1,
        method,
        params,
      }),
    }
  );
  const data = await response.json();
  if (data.error) throw new Error(data.error.message);
  return data.result;
}

const blockNumber = await rpcCall("eth_blockNumber");
const balance = await rpcCall("eth_getBalance", ["0x...", "latest"]);
```

### Python (web3.py)

```python
from web3 import Web3
import os

w3 = Web3(Web3.HTTPProvider(
    f"https://bsc-mainnet.nodereal.io/v1/{os.environ['NODEREAL_API_KEY']}"
))

block_number = w3.eth.block_number
balance = w3.eth.get_balance("0x...")
```
