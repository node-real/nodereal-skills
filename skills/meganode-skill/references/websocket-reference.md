# WebSocket Reference — Real-Time Subscriptions

## Overview

MegaNode provides WebSocket (WSS) connections for real-time blockchain event streaming. Subscriptions push new data to clients as events occur on-chain, eliminating the need for polling.

## WSS Endpoint Format

```
wss://{chain}-{network}.nodereal.io/ws/v1/{API-key}
```

**Examples:**
- BSC Mainnet: `wss://bsc-mainnet.nodereal.io/ws/v1/{API-key}`
- Ethereum: `wss://eth-mainnet.nodereal.io/ws/v1/{API-key}`
- Optimism: `wss://opt-mainnet.nodereal.io/ws/v1/{API-key}`

## Supported Chains for WSS

- BNB Smart Chain
- Ethereum
- Optimism

---

## Subscription Types

### `newHeads`

Notification each time a new block header is appended to the chain, including during chain reorganizations.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "eth_subscribe",
  "params": ["newHeads"]
}
```

**Notification payload:**
```json
{
  "jsonrpc": "2.0",
  "method": "eth_subscription",
  "params": {
    "subscription": "0x...",
    "result": {
      "parentHash": "0x...",
      "sha3Uncles": "0x...",
      "miner": "0x...",
      "stateRoot": "0x...",
      "transactionsRoot": "0x...",
      "receiptsRoot": "0x...",
      "logsBloom": "0x...",
      "difficulty": "0x...",
      "number": "0x...",
      "gasLimit": "0x...",
      "gasUsed": "0x...",
      "timestamp": "0x...",
      "extraData": "0x...",
      "mixHash": "0x...",
      "nonce": "0x...",
      "baseFeePerGas": "0x...",
      "hash": "0x..."
    }
  }
}
```

### `logs`

Filtered event logs from newly imported blocks.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "eth_subscribe",
  "params": [
    "logs",
    {
      "address": "0xContractAddress",
      "topics": ["0xEventSignatureHash"]
    }
  ]
}
```

**Filter parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `address` | `string` or `string[]` | Contract address(es) to filter |
| `topics` | `string[]` | Topic filters for log matching |

**Notification payload:**
```json
{
  "jsonrpc": "2.0",
  "method": "eth_subscription",
  "params": {
    "subscription": "0x...",
    "result": {
      "address": "0x...",
      "topics": ["0x..."],
      "data": "0x...",
      "blockNumber": "0x...",
      "transactionHash": "0x...",
      "transactionIndex": "0x...",
      "blockHash": "0x...",
      "logIndex": "0x...",
      "removed": false
    }
  }
}
```

### `newPendingTransactions`

Notification for each transaction added to the pending state. Returns transaction hash only.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "eth_subscribe",
  "params": ["newPendingTransactions"]
}
```

### `syncing`

Notifications about node sync status changes.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "eth_subscribe",
  "params": ["syncing"]
}
```

---

## Unsubscribe

Cancel a subscription using the subscription ID returned during creation.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "eth_unsubscribe",
  "params": ["0xSubscriptionId"]
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": true
}
```

---

## Billing

WebSocket subscriptions are charged by bandwidth:

**Rate:** `0.04 CU per byte` of data transferred

This applies to all data received through WebSocket subscriptions. Filter subscriptions tightly to minimize costs.

---

## Code Examples

### Node.js with `ws`

```javascript
import WebSocket from "ws";

const ws = new WebSocket(
  `wss://bsc-mainnet.nodereal.io/ws/v1/${process.env.NODEREAL_API_KEY}`
);

ws.on("open", () => {
  console.log("Connected to MegaNode WebSocket");

  // Subscribe to new blocks
  ws.send(JSON.stringify({
    jsonrpc: "2.0",
    id: 1,
    method: "eth_subscribe",
    params: ["newHeads"],
  }));
});

ws.on("message", (data) => {
  const message = JSON.parse(data.toString());

  // Handle subscription confirmation
  if (message.id) {
    console.log("Subscription ID:", message.result);
    return;
  }

  // Handle new block notification
  if (message.params) {
    const block = message.params.result;
    console.log("New block:", parseInt(block.number, 16));
  }
});

ws.on("error", (error) => {
  console.error("WebSocket error:", error);
});

ws.on("close", () => {
  console.log("Connection closed");
  // Implement reconnection logic
});
```

### Monitor ERC-20 Transfer Events

```javascript
const TRANSFER_TOPIC = "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef";

ws.on("open", () => {
  // Subscribe to Transfer events for a specific token
  ws.send(JSON.stringify({
    jsonrpc: "2.0",
    id: 1,
    method: "eth_subscribe",
    params: ["logs", {
      address: "0xTokenContractAddress",
      topics: [TRANSFER_TOPIC],
    }],
  }));
});

ws.on("message", (data) => {
  const message = JSON.parse(data.toString());
  if (message.params?.result) {
    const log = message.params.result;
    const from = "0x" + log.topics[1].slice(26);
    const to = "0x" + log.topics[2].slice(26);
    const value = BigInt(log.data);
    console.log(`Transfer: ${from} → ${to}, Amount: ${value}`);
  }
});
```

### ethers.js WebSocket Provider

```javascript
import { ethers } from "ethers";

const provider = new ethers.WebSocketProvider(
  `wss://bsc-mainnet.nodereal.io/ws/v1/${process.env.NODEREAL_API_KEY}`
);

// Listen for new blocks
provider.on("block", (blockNumber) => {
  console.log("New block:", blockNumber);
});

// Listen for specific contract events
const contract = new ethers.Contract(contractAddress, abi, provider);
contract.on("Transfer", (from, to, value) => {
  console.log(`Transfer: ${from} → ${to}, Value: ${value}`);
});
```

---

## Best Practices

- **Filter tightly:** Use specific `address` and `topics` to reduce bandwidth and CU costs
- **Implement reconnection:** WebSocket connections may drop — always implement auto-reconnect with exponential backoff
- **Handle reorgs:** `newHeads` includes reorganizations — track the `removed` field in log subscriptions
- **Unsubscribe when done:** Clean up subscriptions to stop billing
- **Prefer WSS over polling:** For real-time data, WSS is more efficient than repeated `eth_getLogs` calls
- **Monitor bandwidth:** At 0.04 CU/byte, high-volume subscriptions (e.g., `newPendingTransactions`) can consume CUs quickly
