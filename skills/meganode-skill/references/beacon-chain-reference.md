# ETH Beacon Chain API Reference

## Overview

MegaNode provides Ethereum Beacon Chain (Consensus Layer) API endpoints for interacting with the Ethereum Proof-of-Stake consensus mechanism. This enables querying validator data, block information, attestations, and other consensus-layer state.

---

## Endpoint

```
https://eth-beacon.nodereal.io/v1/{API-key}
```

---

## API Categories

### Beacon — State Queries

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/eth/v1/beacon/genesis` | GET | Get genesis information |
| `/eth/v1/beacon/states/{state_id}/root` | GET | Get state root |
| `/eth/v1/beacon/states/{state_id}/fork` | GET | Get fork information |
| `/eth/v1/beacon/states/{state_id}/finality_checkpoints` | GET | Get finality checkpoints |
| `/eth/v1/beacon/states/{state_id}/validators` | GET | Get validators list |
| `/eth/v1/beacon/states/{state_id}/validators/{validator_id}` | GET | Get specific validator |
| `/eth/v1/beacon/states/{state_id}/validator_balances` | GET | Get validator balances |
| `/eth/v1/beacon/states/{state_id}/committees` | GET | Get committees |

### Beacon — Block Queries

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/eth/v1/beacon/headers` | GET | Get block headers |
| `/eth/v1/beacon/headers/{block_id}` | GET | Get specific block header |
| `/eth/v2/beacon/blocks/{block_id}` | GET | Get full block |
| `/eth/v1/beacon/blocks/{block_id}/root` | GET | Get block root |
| `/eth/v1/beacon/blocks/{block_id}/attestations` | GET | Get block attestations |

### Beacon — Pool

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/eth/v1/beacon/pool/attestations` | GET | Get attestation pool |
| `/eth/v1/beacon/pool/voluntary_exits` | GET | Get voluntary exit pool |

### ValidatorRequiredApi

Required validator endpoints for block production and attestation.

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/eth/v1/validator/blocks/{slot}` | GET | Produce a new block |
| `/eth/v1/validator/attestation_data` | GET | Produce attestation data |
| `/eth/v1/validator/aggregate_attestation` | GET | Get aggregated attestation |
| `/eth/v1/validator/beacon_committee_subscriptions` | POST | Subscribe to committee subnets |
| `/eth/v1/validator/sync_committee_subscriptions` | POST | Subscribe to sync committee subnets |

### Validator Duties

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/eth/v1/validator/duties/attester/{epoch}` | POST | Get attester duties |
| `/eth/v1/validator/duties/proposer/{epoch}` | GET | Get proposer duties |
| `/eth/v1/validator/duties/sync/{epoch}` | POST | Get sync committee duties |

### Node Information

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/eth/v1/node/identity` | GET | Get node identity |
| `/eth/v1/node/peers` | GET | Get connected peers |
| `/eth/v1/node/syncing` | GET | Get sync status |
| `/eth/v1/node/version` | GET | Get node version |
| `/eth/v1/node/health` | GET | Get node health |

### Config

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/eth/v1/config/spec` | GET | Get chain specification |
| `/eth/v1/config/deposit_contract` | GET | Get deposit contract info |
| `/eth/v1/config/fork_schedule` | GET | Get fork schedule |

### Events (SSE)

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/eth/v1/events` | GET (SSE) | Subscribe to events |

**Supported event topics:**
- `head` — New head block
- `block` — New block
- `attestation` — New attestation
- `voluntary_exit` — Voluntary validator exit
- `finalized_checkpoint` — Finalized checkpoint
- `chain_reorg` — Chain reorganization

### Debug

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/eth/v2/debug/beacon/states/{state_id}` | GET | Get full beacon state |
| `/eth/v2/debug/beacon/heads` | GET | Get fork choice heads |

---

## State ID Values

The `{state_id}` parameter accepts:
- `"head"` — Current head of the chain
- `"genesis"` — Genesis state
- `"finalized"` — Last finalized state
- `"justified"` — Last justified state
- Slot number (e.g., `"12345"`)
- State root (hex string)

## Block ID Values

The `{block_id}` parameter accepts:
- `"head"` — Current head block
- `"genesis"` — Genesis block
- `"finalized"` — Last finalized block
- Slot number (e.g., `"12345"`)
- Block root (hex string)

---

## Code Examples

### Query Genesis Info

```javascript
const BEACON_URL = `https://eth-beacon.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`;

const genesis = await fetch(`${BEACON_URL}/eth/v1/beacon/genesis`);
const data = await genesis.json();
console.log("Genesis time:", data.data.genesis_time);
```

### Get Validator Info

```javascript
const validators = await fetch(
  `${BEACON_URL}/eth/v1/beacon/states/head/validators?id=0,1,2`
);
const data = await validators.json();
data.data.forEach(v => {
  console.log(`Validator ${v.index}: ${v.status}, Balance: ${v.balance}`);
});
```

### Subscribe to Events (SSE)

```javascript
const EventSource = require("eventsource");

const es = new EventSource(
  `${BEACON_URL}/eth/v1/events?topics=head,block,finalized_checkpoint`
);

es.addEventListener("head", (event) => {
  const data = JSON.parse(event.data);
  console.log("New head slot:", data.slot);
});

es.addEventListener("finalized_checkpoint", (event) => {
  const data = JSON.parse(event.data);
  console.log("Finalized epoch:", data.epoch);
});
```

---

## Use Cases

- **Staking monitoring:** Track validator performance, balances, and duties
- **Consensus analysis:** Monitor finality, fork choice, and chain health
- **Block production:** Track proposer duties and attestation inclusion
- **Infrastructure health:** Monitor node sync status and peer connectivity
