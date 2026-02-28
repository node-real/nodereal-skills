# Debug & Trace API Reference

## Overview

MegaNode provides Debug and Trace APIs for advanced transaction analysis and smart contract debugging. These are high-CU-cost methods available on Growth tier and above.

---

## Debug API

Standard debug methods for transaction and block tracing.

### `debug_traceTransaction`

Trace a specific transaction execution.

**CU Cost:** 280

```json
{
  "method": "debug_traceTransaction",
  "params": [
    "0xTransactionHash",
    { "tracer": "callTracer" }
  ]
}
```

**Tracer options:**
- `callTracer` — Returns call hierarchy with input/output data
- `prestateTracer` — Returns pre-execution state
- `4byteTracer` — Returns 4-byte function signatures

### `debug_traceCall`

Trace a call without executing it on-chain.

**CU Cost:** 280

```json
{
  "method": "debug_traceCall",
  "params": [
    {
      "from": "0xSender",
      "to": "0xContract",
      "data": "0xMethodCallData"
    },
    "latest",
    { "tracer": "callTracer" }
  ]
}
```

### `debug_traceBlockByNumber`

Trace all transactions in a block by block number.

**CU Cost:** 1,800

```json
{
  "method": "debug_traceBlockByNumber",
  "params": ["0xBlockNumber", { "tracer": "callTracer" }]
}
```

### `debug_traceBlockByHash`

Trace all transactions in a block by block hash.

**CU Cost:** 1,800

```json
{
  "method": "debug_traceBlockByHash",
  "params": ["0xBlockHash", { "tracer": "callTracer" }]
}
```

---

## Debug Pro API

JavaScript-based custom tracing with programmable tracers. Allows writing custom JavaScript tracer logic for advanced analysis.

### `debug_jstraceBlockByNumber`

Execute a JavaScript tracer on all transactions in a block.

**CU Cost:** 18,000

```json
{
  "method": "debug_jstraceBlockByNumber",
  "params": [
    "0xBlockNumber",
    { "tracer": "{ result: [], fault: function() {}, step: function(log) { if(log.op.toString() === 'SSTORE') this.result.push(log.stack.peek(0)); } }" }
  ]
}
```

### `debug_jstraceBlockByHash`

Execute a JavaScript tracer on all transactions in a block by hash.

**CU Cost:** 18,000

```json
{
  "method": "debug_jstraceBlockByHash",
  "params": ["0xBlockHash", { "tracer": "..." }]
}
```

### `debug_jstraceCall`

Execute a JavaScript tracer on a call.

**CU Cost:** 280

```json
{
  "method": "debug_jstraceCall",
  "params": [
    { "from": "0x...", "to": "0x...", "data": "0x..." },
    "latest",
    { "tracer": "..." }
  ]
}
```

### `debug_jstraceTransaction`

Execute a JavaScript tracer on a specific transaction.

**CU Cost:** 280

```json
{
  "method": "debug_jstraceTransaction",
  "params": ["0xTransactionHash", { "tracer": "..." }]
}
```

---

### `eth_getDiffAccounts`

Get accounts that changed state in a block.

**CU Cost:** 1,000–1,200

```json
{
  "method": "eth_getDiffAccounts",
  "params": ["0xBlockNumber"]
}
```

### `eth_getDiffAccountsWithScope`

Get accounts that changed state in a block with scope filter.

**CU Cost:** 1,000–1,200

```json
{
  "method": "eth_getDiffAccountsWithScope",
  "params": ["0xBlockNumber", "0xScope"]
}
```

---

## Trace API

OpenEthereum-compatible trace methods for detailed execution analysis.

### `trace_block`

Returns traces for all transactions in a block.

**CU Cost:** 2,000–2,500

```json
{
  "method": "trace_block",
  "params": ["0xBlockNumber"]
}
```

### `trace_call`

Executes a call and returns the trace without creating a transaction.

**CU Cost:** 2,000–2,500

```json
{
  "method": "trace_call",
  "params": [
    {
      "from": "0xSender",
      "to": "0xContract",
      "data": "0xMethodCallData"
    },
    ["trace"],
    "latest"
  ]
}
```

### `trace_get`

Returns a specific trace by transaction hash and index.

**CU Cost:** 2,000–2,500

```json
{
  "method": "trace_get",
  "params": [
    "0xTransactionHash",
    ["0x0"]
  ]
}
```

### `trace_filter`

Returns traces matching a filter criteria (address, block range, etc.).

**CU Cost:** 10,000

```json
{
  "method": "trace_filter",
  "params": [{
    "fromBlock": "0x...",
    "toBlock": "0x...",
    "toAddress": ["0xContractAddress"],
    "after": 0,
    "count": 100
  }]
}
```

### `trace_transaction`

Returns all traces for a specific transaction.

**CU Cost:** 2,000–2,500

```json
{
  "method": "trace_transaction",
  "params": ["0xTransactionHash"]
}
```

### `trace_replayTransaction`

Replays a transaction with tracing enabled.

**CU Cost:** 2,000–2,500

```json
{
  "method": "trace_replayTransaction",
  "params": [
    "0xTransactionHash",
    ["trace", "vmTrace", "stateDiff"]
  ]
}
```

**Trace types:**
- `trace` — Call graph trace
- `vmTrace` — VM-level execution trace
- `stateDiff` — State changes diff

### `trace_replayBlockTransactions`

Replays all transactions in a block with tracing.

**CU Cost:** 2,000–2,500

```json
{
  "method": "trace_replayBlockTransactions",
  "params": [
    "0xBlockNumber",
    ["trace"]
  ]
}
```

---

## Complete Method Summary

| Category | Method | CU Cost |
|----------|--------|---------|
| **Debug** | `debug_traceTransaction` | 280 |
| **Debug** | `debug_traceCall` | 280 |
| **Debug** | `debug_traceBlockByNumber` | 1,800 |
| **Debug** | `debug_traceBlockByHash` | 1,800 |
| **Debug Pro** | `debug_jstraceBlockByNumber` | 18,000 |
| **Debug Pro** | `debug_jstraceBlockByHash` | 18,000 |
| **Debug Pro** | `debug_jstraceCall` | 280 |
| **Debug Pro** | `debug_jstraceTransaction` | 280 |
| **Trace** | `trace_block` | 2,000–2,500 |
| **Trace** | `trace_call` | 2,000–2,500 |
| **Trace** | `trace_get` | 2,000–2,500 |
| **Trace** | `trace_filter` | 10,000 |
| **Trace** | `trace_transaction` | 2,000–2,500 |
| **Trace** | `trace_replayTransaction` | 2,000–2,500 |
| **Trace** | `trace_replayBlockTransactions` | 2,000–2,500 |

---

## Supported Chains

| Chain | Debug API | Debug Pro API | Trace API |
|-------|-----------|---------------|-----------|
| BNB Smart Chain | Yes | Yes | Yes |
| Ethereum | Yes | Yes | Yes |
| Optimism | Partial | — | Partial |

## Notes

- Debug/Trace APIs require **Growth tier or above**
- These methods are expensive — avoid in production hot paths
- `trace_filter` is the most expensive at 10,000 CU per call
- Use `callTracer` for most debugging needs (lower overhead than vmTrace)
