# MegaFuel Reference — Gasless Transactions

## Overview

MegaFuel is NodeReal's paymaster implementation based on BNB Chain's BEP-322 Proposer-Builder Separation (PBS) architecture. It enables gas fee sponsorship for EOA wallets on BSC and opBNB, allowing dApps to cover transaction costs for their users.

The MegaFuel API is organized into three sub-sections (matching the NodeReal API Reference sidebar):

- **Sponsor API** — Core transaction sponsorship: `pm_sponsorable`, `eth_sendRawTransaction`, `eth_getGaslessTransactionByHash`
- **Public Policy API** — Public whitelist management: `pm_addToWhitelist`, `pm_rmFromWhitelist`, `pm_emptyWhitelist`, `pm_getWhitelist`
- **Private Policy API** — Private/restricted policy management with the same whitelist methods, scoped to private sponsor policies

## Paymaster Endpoints

| Network | Endpoint |
|---------|----------|
| BSC Mainnet | `https://bsc-megafuel.nodereal.io/` |
| BSC Testnet | `https://bsc-megafuel-testnet.nodereal.io/` |
| opBNB Mainnet | `https://opbnb-megafuel.nodereal.io/` |
| opBNB Testnet | `https://opbnb-megafuel-testnet.nodereal.io/` |

## SDKs

- **JavaScript SDK:** [megafuel-js-sdk](https://github.com/node-real/megafuel-js-sdk)
- **Go SDK:** [megafuel-go-sdk](https://github.com/node-real/megafuel-go-sdk)

---

## Core API Methods

### `pm_sponsorable`

Check if a transaction is eligible for gas sponsorship.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "pm_sponsorable",
  "params": [{
    "from": "0xSenderAddress",
    "to": "0xContractAddress",
    "value": "0x0",
    "data": "0xMethodCallData"
  }]
}
```

**Response (success):**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "is_sponsorable": true,
    "sponsor_name": "MyDApp Sponsor"
  }
}
```

**Response (not sponsorable):**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "is_sponsorable": false,
    "sponsor_name": ""
  }
}
```

### `eth_sendRawTransaction` (via MegaFuel)

Submit a signed zero-gas transaction to the MegaFuel endpoint.

**Required header:** `User-Agent: your-wallet-name/v1.0.0`

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "eth_sendRawTransaction",
  "params": ["0xSignedTransactionHex"]
}
```

### `eth_getTransactionCount` (via MegaFuel)

Get the correct nonce from the MegaFuel endpoint to prevent nonce mismatch.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "eth_getTransactionCount",
  "params": ["0xSenderAddress", "latest"]
}
```

### `eth_getGaslessTransactionByHash`

Query the precise status of a gasless transaction.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "eth_getGaslessTransactionByHash",
  "params": ["0xTransactionHash"]
}
```

---

## Complete Integration Workflow

### Step 1: Check Sponsorability

```javascript
async function checkSponsorable(megafuelUrl, txParams) {
  const response = await fetch(megafuelUrl, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      jsonrpc: "2.0",
      id: 1,
      method: "pm_sponsorable",
      params: [txParams],
    }),
  });
  const data = await response.json();
  return data.result;
}
```

### Step 2: Build and Sign Zero-Gas Transaction

```javascript
import { ethers } from "ethers";

async function buildGaslessTx(megafuelUrl, wallet, to, data) {
  // Get nonce from MegaFuel endpoint
  const nonceResponse = await fetch(megafuelUrl, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      jsonrpc: "2.0",
      id: 1,
      method: "eth_getTransactionCount",
      params: [wallet.address, "latest"],
    }),
  });
  const nonceData = await nonceResponse.json();

  const tx = {
    to,
    data,
    value: 0,
    gasPrice: 0,          // Zero gas price for sponsored tx
    gasLimit: 500000,      // Estimate appropriately
    nonce: parseInt(nonceData.result, 16),
    chainId: 56,           // BSC mainnet
  };

  return wallet.signTransaction(tx);
}
```

### Step 3: Submit via MegaFuel

```javascript
async function sendGaslessTx(megafuelUrl, signedTx) {
  const response = await fetch(megafuelUrl, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "User-Agent": "my-dapp/v1.0.0",  // Required header
    },
    body: JSON.stringify({
      jsonrpc: "2.0",
      id: 1,
      method: "eth_sendRawTransaction",
      params: [signedTx],
    }),
  });
  return response.json();
}
```

### Step 4: Monitor Transaction

```javascript
async function monitorGaslessTx(megafuelUrl, txHash, timeoutMs) {
  const start = Date.now();
  while (Date.now() - start < timeoutMs) {
    const response = await fetch(megafuelUrl, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        jsonrpc: "2.0",
        id: 1,
        method: "eth_getGaslessTransactionByHash",
        params: [txHash],
      }),
    });
    const data = await response.json();
    if (data.result && data.result.status) {
      return data.result;
    }
    await new Promise(resolve => setTimeout(resolve, 3000));
  }
  throw new Error("Transaction timeout");
}
```

---

## Timeout Thresholds

| Network | Timeout |
|---------|---------|
| BSC | 120 seconds |
| opBNB | 42 seconds |

If a transaction is not mined within the timeout threshold, consider it failed and implement fallback to standard (gas-paying) transaction submission.

---

## Sponsor Policy Management

Policy management endpoint: `https://open-platform-ap.nodereal.io/{apikey}/megafuel`

### Whitelist Types

| Whitelist | Purpose | Example |
|-----------|---------|---------|
| `FromAccountWhitelist` | Only specified sender addresses get sponsorship | Limit to known user wallets |
| `ToAccountWhitelist` | Restricts which contracts can be interacted with | Only sponsor your own contracts |
| `ContractMethodSigWhitelist` | Limits callable contract methods | Only allow `transfer()` = `0xa9059cbb` |
| `BEP20ReceiverWhitelist` | Specifies allowed token receiver addresses | Limit token transfer recipients |

### `pm_addToWhitelist`

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "pm_addToWhitelist",
  "params": [{
    "WhitelistType": "ToAccountWhitelist",
    "Values": ["0xContractAddress1", "0xContractAddress2"]
  }]
}
```

### `pm_rmFromWhitelist`

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "pm_rmFromWhitelist",
  "params": [{
    "WhitelistType": "ToAccountWhitelist",
    "Values": ["0xContractAddress1"]
  }]
}
```

### `pm_emptyWhitelist`

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "pm_emptyWhitelist",
  "params": [{
    "WhitelistType": "ToAccountWhitelist"
  }]
}
```

### `pm_getWhitelist`

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "pm_getWhitelist",
  "params": [{
    "WhitelistType": "ToAccountWhitelist",
    "Offset": 0,
    "Limit": 100
  }]
}
```

---

## Sponsor Registration Process

1. Create MegaNode account at https://nodereal.io/meganode
2. Submit application via the registration form
3. Approval takes 2-3 business days
4. Receive testnet policy (pre-funded with 10 BNB) and mainnet policy
5. Fund mainnet policy by sending BNB to: `0x855bcfFbDcD35c52EAD99609f5e7ABE211EB4e88`

---

## Best Practices

- Always check `pm_sponsorable` before setting gas price to zero
- Display `sponsor_name` to users for transparency
- Implement fallback to standard transaction on sponsorship failure
- Get nonce from MegaFuel endpoint, not standard RPC, to avoid nonce conflicts
- Always include `User-Agent` header with wallet/dApp identifier
- Implement timeout-based failure detection (120s BSC, 42s opBNB)

---

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| `pm_sponsorable` returns `false` | No matching sponsor policy for this transaction | Check policy configuration; verify contract/method whitelist |
| Transaction stuck pending | Not mined within timeout (120s BSC, 42s opBNB) | Consider transaction failed; retry or fall back to standard tx |
| Nonce conflict errors | Using nonce from standard RPC | Always get nonce from MegaFuel endpoint |
| Missing `User-Agent` header | Required for MegaFuel requests | Include `User-Agent: your-dapp/version` in all requests |
| Policy creation fails | Insufficient balance or invalid parameters | Check sponsor account balance; verify policy JSON format |
| Gasless tx reverts | Contract logic issue, not gas related | Debug the contract call separately with `eth_call` |

## Documentation

- **MegaFuel Guide:** https://docs.nodereal.io/docs/megafuel
- **BEP-322 Specification:** https://github.com/bnb-chain/BEPs/blob/master/BEPs/BEP-322.md
- **API Reference:** https://docs.nodereal.io/reference
