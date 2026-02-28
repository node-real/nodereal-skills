# Non-EVM Chains API Reference

## Overview

MegaNode supports several non-EVM blockchain networks, each with their own API protocols. This reference covers Aptos, NEAR, Bitcoin, TRON, Sui, and Cosmos APIs available through MegaNode.

---

## Aptos Node API

**Endpoint:** `https://aptos-mainnet.nodereal.io/v1/{API-key}`

Aptos uses a REST API (not JSON-RPC).

### Get Started

The Aptos Node API base URL: `https://aptos-mainnet.nodereal.io/v1/{API-key}`

### Key Endpoints

Categories: **General**, **Accounts**, **Blocks**, **Events**, **Tables**, **Transactions**

| Endpoint | Method | Category | Description |
|----------|--------|----------|-------------|
| `/v1/` | GET | General | Get ledger info |
| `/v1/-/healthy` | GET | General | Health check |
| `/v1/accounts/{address}` | GET | Get account info |
| `/v1/accounts/{address}/resources` | GET | Get account resources |
| `/v1/accounts/{address}/modules` | GET | Get account modules |
| `/v1/blocks/by_height/{height}` | GET | Get block by height |
| `/v1/blocks/by_version/{version}` | GET | Get block by version |
| `/v1/transactions` | GET | List transactions |
| `/v1/transactions/by_hash/{hash}` | GET | Get transaction by hash |
| `/v1/transactions/by_version/{version}` | GET | Get transaction by version |
| `/v1/transactions` | POST | Submit transaction |
| `/v1/transactions/simulate` | POST | Simulate transaction |
| `/v1/events/{event_handle}/{field_name}` | GET | Get events |
| `/v1/tables/{table_handle}/item` | POST | Get table item |

### Example

```javascript
const APTOS_URL = `https://aptos-mainnet.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`;

// Get account resources
const resources = await fetch(`${APTOS_URL}/v1/accounts/0x1/resources`);
const data = await resources.json();

// Get ledger info
const ledger = await fetch(`${APTOS_URL}/v1/`);
const ledgerData = await ledger.json();
console.log("Block height:", ledgerData.block_height);
```

### Using Aptos SDK

```javascript
import { Aptos, AptosConfig, Network } from "@aptos-labs/ts-sdk";

const config = new AptosConfig({
  fullnodeUrl: `https://aptos-mainnet.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`,
});
const aptos = new Aptos(config);

const balance = await aptos.getAccountResource({
  accountAddress: "0x1",
  resourceType: "0x1::coin::CoinStore<0x1::aptos_coin::AptosCoin>",
});
```

---

## NEAR RPC

**Endpoint:** `https://near-mainnet.nodereal.io/v1/{API-key}`

NEAR uses JSON-RPC 2.0.

### Key Methods

| Method | Description |
|--------|-------------|
| `query` | Query view access key, account, contract state |
| `block` | Get block by height or hash |
| `chunk` | Get chunk details |
| `tx` | Get transaction status |
| `EXPERIMENTAL_changes` | Get state changes |
| `EXPERIMENTAL_tx_status` | Get detailed transaction status |
| `status` | Get node status |
| `validators` | Get validator info for an epoch |

### Example

```javascript
const NEAR_URL = `https://near-mainnet.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`;

// Query account
const response = await fetch(NEAR_URL, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    jsonrpc: "2.0",
    id: 1,
    method: "query",
    params: {
      request_type: "view_account",
      finality: "final",
      account_id: "example.near",
    },
  }),
});
```

---

## Bitcoin RPC

**Endpoint:** `https://btc-mainnet.nodereal.io/v1/{API-key}`

Bitcoin uses JSON-RPC (Bitcoin Core compatible).

### Key Methods

| Method | Description |
|--------|-------------|
| `getblockchaininfo` | Get blockchain state info |
| `getblock` | Get block by hash |
| `getblockcount` | Get current block height |
| `getblockhash` | Get block hash by height |
| `getrawtransaction` | Get raw transaction data |
| `gettxout` | Get UTXO info |
| `getmempoolinfo` | Get mempool statistics |
| `estimatesmartfee` | Estimate transaction fee |

### Example

```javascript
const BTC_URL = `https://btc-mainnet.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`;

// Get block count
const response = await fetch(BTC_URL, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    jsonrpc: "1.0",
    id: 1,
    method: "getblockcount",
    params: [],
  }),
});
const data = await response.json();
console.log("Bitcoin block height:", data.result);

// Get raw transaction
const txResponse = await fetch(BTC_URL, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    jsonrpc: "1.0",
    id: 1,
    method: "getrawtransaction",
    params: ["txid_here", true],  // true for decoded
  }),
});
```

---

## TRON HTTP API

**Endpoint:** `https://tron-mainnet.nodereal.io/v1/{API-key}`

TRON uses a REST/HTTP API (not JSON-RPC).

### Key Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/wallet/getnowblock` | GET | Get latest block |
| `/wallet/getblockbynum` | POST | Get block by number |
| `/wallet/getaccount` | POST | Get account info |
| `/wallet/gettransactionbyid` | POST | Get transaction by ID |
| `/wallet/gettransactioninfobyid` | POST | Get transaction info |
| `/wallet/broadcasttransaction` | POST | Broadcast signed transaction |
| `/wallet/triggersmartcontract` | POST | Call smart contract |
| `/wallet/triggerconstantcontract` | POST | Read-only contract call |

### Example

```javascript
const TRON_URL = `https://tron-mainnet.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`;

// Get latest block
const block = await fetch(`${TRON_URL}/wallet/getnowblock`);
const blockData = await block.json();
console.log("TRON block:", blockData.block_header.raw_data.number);

// Get account info
const account = await fetch(`${TRON_URL}/wallet/getaccount`, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    address: "TRX_ADDRESS_HERE",
    visible: true,
  }),
});
```

---

## Sui JSON-RPC

**Endpoint:** `https://sui-mainnet.nodereal.io/v1/{API-key}`

Sui uses JSON-RPC 2.0.

### Key Methods

| Method | Description |
|--------|-------------|
| `sui_getObject` | Get object by ID |
| `sui_multiGetObjects` | Batch get objects |
| `sui_getOwnedObjects` | Get objects owned by address |
| `sui_getTotalTransactionBlocks` | Get total transaction count |
| `sui_getTransactionBlock` | Get transaction by digest |
| `sui_multiGetTransactionBlocks` | Batch get transactions |
| `sui_getLatestCheckpointSequenceNumber` | Get latest checkpoint |
| `sui_getCheckpoint` | Get checkpoint by sequence number |
| `suix_getBalance` | Get coin balance for address |
| `suix_getAllBalances` | Get all coin balances |
| `suix_getCoins` | Get coin objects for address |
| `suix_getReferenceGasPrice` | Get current gas price |

### Example

```javascript
const SUI_URL = `https://sui-mainnet.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`;

// Get balance
const response = await fetch(SUI_URL, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    jsonrpc: "2.0",
    id: 1,
    method: "suix_getBalance",
    params: ["0xAddress", "0x2::sui::SUI"],
  }),
});
```

---

## Cosmos (Tendermint RPC)

**Endpoint:** `https://cosmos-mainnet.nodereal.io/v1/{API-key}`

Cosmos uses Tendermint RPC.

### Key Endpoints

| Endpoint | Description |
|----------|-------------|
| `/status` | Get node status |
| `/block` | Get latest block |
| `/block?height=N` | Get block at height N |
| `/blockchain?minHeight=X&maxHeight=Y` | Get block range |
| `/validators` | Get current validator set |
| `/tx?hash=0xHASH` | Get transaction by hash |
| `/tx_search?query="..."` | Search transactions |
| `/abci_query` | Query application state |

### Example

```javascript
const COSMOS_URL = `https://cosmos-mainnet.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`;

// Get node status
const status = await fetch(`${COSMOS_URL}/status`);
const data = await status.json();
console.log("Latest block:", data.result.sync_info.latest_block_height);
```

---

## Notes

- Non-EVM chains do NOT support Enhanced APIs (`nr_`/`ng_` methods)
- Archive node access varies by chain — check [supported-chains.md](supported-chains.md)
- Each chain has its own SDK ecosystem — NodeReal endpoints are compatible with standard chain SDKs
- CU costs for non-EVM methods may differ from EVM methods — check the MegaNode dashboard
