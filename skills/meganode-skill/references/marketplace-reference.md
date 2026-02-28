# API Marketplace Reference

## Overview

NodeReal operates an API Marketplace offering both first-party enhanced APIs and third-party integrations. These extend MegaNode's core RPC functionality with specialized data services for NFTs, DeFi, identity, and more.

All marketplace APIs are accessed through the open platform endpoint:

```
https://open-platform.nodereal.io/{apiKey}/{service-name}/{method-path}
```

**Activation:** Marketplace APIs must be activated in the MegaNode dashboard before use. Some require additional subscription fees.

---

## 1. NFTScan API

Comprehensive NFT data service. Base endpoint:

```
https://open-platform.nodereal.io/{apiKey}/nftscan
```

**Supported chains** (pass as `{chain_name}` path parameter):
`eth`, `bnb`, `polygon`, `moonbeam`, `arbitrum`, `optimism`, `platon`, `avalanche`, `cronos`, `fantom`, `gnosis`

---

### 1.1 NFT Asset Queries

#### Get NFTs by account

Returns NFTs owned by an account address.

```
GET /api/v2/account/own/{account_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| account_address | path | yes | Owner wallet address |
| chain_name | path | yes | Chain to query |
| erc_type | query | no | `erc721` or `erc1155` |
| contract_address | query | no | Filter by NFT contract |
| cursor | query | no | Pagination cursor |
| limit | query | no | Page size (default 20, max 100) |
| show_attribute | query | no | `true` or `false` |
| sort_field | query | no | `mint_time`, `own_time`, `latest_trade_price` |
| sort_direction | query | no | `asc` or `desc` |

**Response example:**
```json
{
  "code": 200,
  "data": {
    "content": [
      {
        "amount": "1",
        "contract_address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d",
        "contract_name": "BoredApeYachtClub",
        "token_id": "1",
        "erc_type": "erc721",
        "name": "#1",
        "image_uri": "QmPbxeGcXhYQQNgsC6a36dDyYUcHgMLnGKnF8pVFmGsvqi",
        "owner": "0xaba7161a7fb69c88e16ed9f455ce62b791ee4d03",
        "latest_trade_price": 38.77,
        "latest_trade_symbol": "WETH",
        "mint_price": 0.7,
        "mint_timestamp": 1619133220000,
        "rarity_rank": 2668,
        "rarity_score": 1.06,
        "attributes": [
          {
            "attribute_name": "Background",
            "attribute_value": "Yellow",
            "percentage": "16.49%"
          }
        ]
      }
    ],
    "next": "NS871DD6359B64187E",
    "total": 30000
  },
  "msg": ""
}
```

---

#### Get all NFTs by account

Returns all NFTs owned by an account, grouped by contract address.

```
GET /api/v2/account/own/all/{account_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| account_address | path | yes | Owner wallet address |
| chain_name | path | yes | Chain to query |
| erc_type | query | no | `erc721` or `erc1155` |
| show_attribute | query | no | `true` or `false` |

**Response:** Array of `CollectionAssetModel` objects, each containing `assets[]`, `contract_address`, `contract_name`, `floor_price`, `items_total`, `owns_total`, `symbol`.

---

#### Get minted NFTs by account

Returns NFTs minted by an account address.

```
GET /api/v2/account/mint/{account_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| account_address | path | yes | Minter wallet address |
| chain_name | path | yes | Chain to query |
| erc_type | query | no | `erc721` or `erc1155` |
| contract_address | query | no | Filter by NFT contract |
| cursor | query | no | Pagination cursor |
| limit | query | no | Page size (default 20, max 100) |
| show_attribute | query | no | `true` or `false` |

---

#### Get NFTs by contract

Returns NFTs belonging to a contract, sorted by token_id ascending.

```
GET /api/v2/assets/{contract_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| contract_address | path | yes | NFT contract address |
| chain_name | path | yes | Chain to query |
| cursor | query | no | Pagination cursor |
| limit | query | no | Page size (default 20, max 100) |
| show_attribute | query | no | `true` or `false` |

---

#### Get single NFT

Returns a single NFT asset.

```
GET /api/v2/assets/{contract_address}/{token_id}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| contract_address | path | yes | NFT contract address |
| token_id | path | yes | Token ID (Hex or Number) |
| chain_name | path | yes | Chain to query |
| show_attribute | query | no | `true` or `false` |

**Response example:**
```json
{
  "code": 200,
  "data": {
    "amount": "1",
    "contract_address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d",
    "contract_name": "BoredApeYachtClub",
    "token_id": "1",
    "erc_type": "erc721",
    "name": "#1",
    "owner": "0xaba7161a7fb69c88e16ed9f455ce62b791ee4d03",
    "mint_price": 0.7,
    "latest_trade_price": 38.77,
    "rarity_rank": 2668
  },
  "msg": ""
}
```

---

#### Get multiple NFTs

Returns multiple NFTs by contract address + token ID pairs.

```
POST /api/v2/assets/batch/{chain_name}
```

**Request body:**
```json
{
  "contract_address_with_token_id_list": [
    {
      "contract_address": "0x3e855b7941fe8ef5f07dad68c5140d6a3ec1b286",
      "token_id": "0x000000000000000000000000000000000000000000000000000000000000013a"
    }
  ],
  "show_attribute": false
}
```

**Response:** Array of `AssetModel` objects.

---

#### Search NFTs

Returns NFTs matching search filters. Block number range cannot exceed 10,000.

```
POST /api/v2/assets/filters/{chain_name}
```

**Request body:**
```json
{
  "block_number_start": 14000000,
  "block_number_end": 15000000,
  "contract_address_list": [],
  "cursor": "100",
  "limit": 20,
  "show_attribute": false
}
```

---

#### Get NFTs by attributes

Returns NFTs matching specific attribute filters.

```
POST /api/v2/assets/attributes/{chain_name}
```

**Request body:**
```json
{
  "attributes": [
    {
      "attribute_name": "Fur",
      "attribute_values": "Black,Blue"
    }
  ],
  "contract_address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d",
  "cursor": "100",
  "limit": 20,
  "show_attribute": false
}
```

---

#### Get all multi-chain NFTs by account

Returns all NFTs owned across multiple chains, grouped by chain and contract.

```
GET /api/v2/assets/chain/{account_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| account_address | path | yes | Owner wallet address |
| chain_name | path | yes | Chain to query |
| chain | query | yes | Chain abbreviations separated by `;` (e.g., `eth;bnb`) |
| erc_type | query | no | `erc721` or `erc1155` |

**Response:** Array of `{ chain, collection_assets[] }` objects.

---

### 1.2 NFT Transaction Queries

#### Get transactions by account

```
GET /api/v2/transactions/account/{account_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| account_address | path | yes | Account address |
| chain_name | path | yes | Chain to query |
| contract_address | query | no | Filter by NFT contract |
| token_id | query | no | Filter by token ID |
| event_type | query | no | `Mint`, `Transfer`, `Sale`, `Burn` (separate multiple with `;`) |
| cursor | query | no | Pagination cursor |
| limit | query | no | Page size (default 20, max 100) |
| sort_direction | query | no | `asc` or `desc` |

**Response example:**
```json
{
  "code": 200,
  "data": {
    "content": [
      {
        "aggregate_exchange_name": "OpenSea",
        "amount": "1",
        "block_number": 12344148,
        "contract_address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d",
        "contract_name": "BoredApeYachtClub",
        "erc_type": "erc721",
        "event_type": "Transfer",
        "exchange_name": "OpenSea",
        "from": "0xaba7161a7fb69c88e16ed9f455ce62b791ee4d03",
        "hash": "0xe93e858f9330afa4581e260198195623aa7f5cd2809012440ea291d317be9f2f",
        "token_id": "1",
        "trade_price": 0.1,
        "trade_symbol": "ETH",
        "timestamp": 1619133220000
      }
    ],
    "next": "NS871DD6359B64187E",
    "total": 30000
  },
  "msg": ""
}
```

---

#### Get transactions by contract

```
GET /api/v2/transactions/{contract_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| contract_address | path | yes | NFT contract address |
| chain_name | path | yes | Chain to query |
| event_type | query | no | `Mint;Transfer;Sale;Burn` |
| cursor | query | no | Pagination cursor |
| limit | query | no | Page size (default 20, max 100) |
| sort_direction | query | no | `asc` or `desc` |

---

#### Get transactions by NFT

```
GET /api/v2/transactions/{contract_address}/{token_id}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| contract_address | path | yes | NFT contract address |
| token_id | path | yes | Token ID (Hex or Number) |
| chain_name | path | yes | Chain to query |
| event_type | query | no | `Mint;Transfer;Sale;Burn` |
| cursor | query | no | Pagination cursor |
| limit | query | no | Page size (default 20, max 100) |
| sort_direction | query | no | `asc` or `desc` |

---

#### Get transactions by to address

```
GET /api/v2/transactions/to/{to_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| to_address | path | yes | The `to` address of the transaction |
| chain_name | path | yes | Chain to query |
| event_type | query | no | `Mint;Transfer;Sale;Burn` |
| cursor | query | no | Pagination cursor |
| limit | query | no | Page size (default 20, max 100) |
| sort_direction | query | no | `asc` or `desc` |

---

#### Get transactions by hash

```
POST /api/v2/transactions/txhash/{chain_name}
```

**Request body:**
```json
{
  "event_type": "",
  "tx_hash_list": [
    "0xe93e858f9330afa4581e260198195623aa7f5cd2809012440ea291d317be9f2f"
  ]
}
```

Maximum 50 transaction hashes per request. Response is a flat array of `TransactionModel` objects.

---

#### Search transactions

Returns NFT transactions matching search filters. Block number range cannot exceed 10,000.

```
POST /api/v2/transactions/filters/{chain_name}
```

**Request body:**
```json
{
  "block_number_start": 14000000,
  "block_number_end": 15000000,
  "contract_address_list": [],
  "cursor": "100",
  "event_type": "",
  "limit": 20
}
```

---

### 1.3 NFT Collection Queries

#### Get an NFT collection

Returns information for a collection by contract address.

```
GET /api/v2/collections/{contract_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| contract_address | path | yes | NFT contract address |
| chain_name | path | yes | Chain to query |
| show_attribute | query | no | Whether to include attribute distribution |

**Response example:**
```json
{
  "code": 200,
  "data": {
    "contract_address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d",
    "name": "BoredApeYachtClub",
    "symbol": "BAYC",
    "erc_type": "erc721",
    "items_total": 10000,
    "owners_total": 6271,
    "floor_price": 1.113,
    "opensea_floor_price": 91,
    "opensea_verified": true,
    "royalty": 250,
    "deploy_block_number": 12292922,
    "description": "The Bored Ape Yacht Club is a collection of 10,000 unique Bored Ape NFTs...",
    "logo_url": "https://logo.nftscan.com/logo/0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d.png",
    "website": "http://www.boredapeyachtclub.com/",
    "twitter": "BoredApeYC",
    "discord": "https://discord.gg/3P5K3dzgdB"
  },
  "msg": ""
}
```

---

#### Search NFT collections

Returns collections matching search filters.

```
POST /api/v2/collections/filters/{chain_name}
```

**Request body:**
```json
{
  "block_number_start": 14000000,
  "block_number_end": 15000000,
  "contract_address_list": [],
  "limit": 20,
  "name": "BoredApeYachtClub",
  "name_fuzzy_search": false,
  "offset": 0,
  "show_collection": false,
  "sort_direction": "desc",
  "sort_field": "floor_price",
  "symbol": "BAYC",
  "twitter": "BoredApeYC"
}
```

---

#### Get NFT collections by account

Returns collections owned by an account, sorted by floor_price descending.

```
GET /api/v2/collections/own/{account_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| account_address | path | yes | Account address |
| chain_name | path | yes | Chain to query |
| erc_type | query | yes | `erc721` or `erc1155` |
| offset | query | no | Pagination offset |
| limit | query | no | Page size (default 20, max 100) |

---

#### Get NFT collections by ranking

Returns ranked collections by trading metrics.

```
GET /api/v2/collections/rankings/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| chain_name | path | yes | Chain to query |
| sort_field | query | no | `volume_total`, `sales_total`, `average_price`, `floor_price`, `volume_1d`, `volume_7d`, `volume_change_1d`, `volume_change_7d`, `average_price_change_1d`, `average_price_change_7d` |
| sort_direction | query | no | `asc` or `desc` |
| limit | query | no | Result size (default 100, max 500) |

**Response includes:** `average_price`, `volume_total`, `sales_total`, `floor_price` plus all standard collection fields.

---

### 1.4 Rankings and Statistics

#### Trade Ranking

```
GET /api/v2/statistics/ranking/trade/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| chain_name | path | yes | Chain to query |
| time | query | no | `1d`, `15m`, `30m`, `1h`, `6h`, `12h` |
| sort_field | query | no | `volume` or `sales` |
| sort_direction | query | no | `asc` or `desc` |
| show_7d_trends | query | no | Whether to include 7-day trend data |

**Response example:**
```json
{
  "code": 200,
  "data": [
    {
      "contract_address": "0x307135a29962f0b338c0103e06e8e7d03bd7267f",
      "contract_name": "Doodles",
      "volume": 4577.1396,
      "sales": 20,
      "average_price": 1.13,
      "floor_price": 1.12,
      "highest_price": 20,
      "lowest_price": 1.113,
      "market_cap": 55288,
      "volume_change": "53%",
      "sales_change": "10%"
    }
  ],
  "msg": ""
}
```

---

#### Mint Ranking

```
GET /api/v2/statistics/ranking/mint/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| chain_name | path | yes | Chain to query |
| time | query | no | `1d`, `15m`, `30m`, `1h`, `6h`, `12h`, `3d` |

**Response example:**
```json
{
  "code": 200,
  "data": [
    {
      "contract_address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d",
      "contract_name": "BoredApeYachtClub",
      "floor_price": 1.113,
      "mint_cost": 0.08,
      "mint_total": 10000,
      "mint_total_change": "53%",
      "sale_total": 25
    }
  ],
  "msg": ""
}
```

---

#### Collection Ranking

```
GET /api/v2/statistics/ranking/collection/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| chain_name | path | yes | Chain to query |
| sort_field | query | no | `volume_1d`, `volume_7d`, `volume_30d`, `volume_total`, `volume_change_1d`, `volume_change_7d`, `volume_change_30d`, `sales_1d`, `sales_7d`, `sales_30d`, `sales_total`, `sales_change_1d`, `sales_change_7d`, `sales_change_30d`, `floor_price`, `market_cap` |
| sort_direction | query | no | `asc` or `desc` |
| limit | query | no | Result size (default 20, max 100) |

**Response includes:** `volume_1d`, `volume_7d`, `volume_30d`, `volume_total`, `sales_1d`, `sales_7d`, `sales_30d`, `sales_total`, `average_price_1d`, `market_cap`, plus growth rate fields.

---

#### Collection Statistics

```
GET /api/v2/statistics/collection/{contract_address}/{chain_name}
```

**Response example:**
```json
{
  "code": 200,
  "data": {
    "contract_address": "0x8a90cab2b38dba80c64b7734e58ee1db38b8992e",
    "contract_name": "Doodles",
    "erc_type": "erc721",
    "floor_price": 1.113,
    "highest_price": 296.69,
    "items_total": 10000,
    "market_cap": 68642,
    "owners_total": 6271,
    "sales_24h": 3,
    "total_volume": 94607.141,
    "volume_1d": 762.3803,
    "volume_7d": 5705.4187,
    "volume_30d": 18857.0902,
    "average_price_24h": 10.134,
    "next_blue_chip_probability": "52.22%"
  },
  "msg": ""
}
```

---

#### Collection Trade Distribution

```
GET /api/v2/statistics/collection/trade/{contract_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| contract_address | path | yes | NFT contract address |
| chain_name | path | yes | Chain to query |
| time | query | no | `1d`, `1h`, `4h`, `12h`, `3d`, `7d`, `30d`, `90d` |

**Response example:**
```json
{
  "code": 200,
  "data": [
    {
      "exchange_name": "Opensea",
      "timestamp": 1658729641000,
      "trade_price": 0.1,
      "transaction_hash": "0x382df08489c766cabf26526db6570927b9df7d7fdc41d866d3a7cb784c1fcba0"
    }
  ],
  "msg": ""
}
```

---

#### Collection Trending Statistics

```
GET /api/v2/statistics/collection/trending/{contract_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| contract_address | path | yes | NFT contract address |
| chain_name | path | yes | Chain to query |
| time | query | no | `1d`, `1h`, `4h`, `12h`, `3d`, `7d`, `30d`, `90d`, `all` |

**Response example:**
```json
{
  "code": 200,
  "data": [
    {
      "average_price": 0.2542,
      "begin_timestamp": 1658734117714,
      "end_timestamp": 1658735117714,
      "volume": 22.8775
    }
  ],
  "msg": ""
}
```

---

#### Collection Overview

```
GET /api/v2/statistics/collection/overview/{chain_name}
```

**Response example:**
```json
{
  "code": 200,
  "data": [
    {
      "contract_address": "0x8a90cab2b38dba80c64b7734e58ee1db38b8992e",
      "contract_name": "Doodles",
      "floor_price": 6.6952
    }
  ],
  "msg": ""
}
```

---

#### Blue Chip

Returns blue chip statistics for a collection.

```
GET /api/v2/statistics/blue/chip/{contract_address}/{chain_name}
```

**Response example:**
```json
{
  "code": 200,
  "data": {
    "blue_chip_owner": 5638,
    "next_blue_chip_probability": "88.11%",
    "owner": 6394
  },
  "msg": ""
}
```

---

### 1.5 Account and Collection Analytics

#### Account Overview

```
GET /api/v2/statistics/overview/{account_address}/{chain_name}
```

**Response example:**
```json
{
  "code": 200,
  "data": {
    "bought_count": 1051,
    "bought_value": 148649.85,
    "bought_value_usdt": 246352936.91,
    "burn_count": 0,
    "gas_value": 6.6,
    "gas_value_usdt": 10941,
    "holding_count": 49,
    "holding_value": 9.97,
    "holding_value_usdt": 16522.98,
    "mint_count": 34,
    "receive_count": 22,
    "send_count": 3,
    "sold_count": 1050,
    "sold_value": 148886.72,
    "sold_value_usdt": 246745494
  },
  "msg": ""
}
```

---

#### Account Holding Distribution

```
GET /api/v2/statistics/distribution/{account_address}/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| account_address | path | yes | Account address |
| chain_name | path | yes | Chain to query |
| distribution_type | query | no | Default: `volume` |

**Response:** Distribution array with `contract`, `name`, `proportion`, `value` for each held collection.

---

### 1.6 Utilities

#### Get NFT amount by account

```
POST /api/v2/asset/account/amount/{chain_name}
```

**Request body:**
```json
{
  "account_address_list": ["0xe525fae3fc6fbb23af05e54ff413613a6573cff2"]
}
```

Maximum 50 addresses. **Response:**
```json
{
  "code": 200,
  "data": [
    {
      "account_address": "0xe525fae3fc6fbb23af05e54ff413613a6573cff2",
      "erc721_items_total": 100,
      "erc1155_items_total": 20,
      "items_total": 120
    }
  ],
  "msg": ""
}
```

---

#### Get NFT owners by contract (ERC721)

```
GET /api/v2/asset/collection/amount/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| chain_name | path | yes | Chain to query |
| contract_address | query | yes | NFT contract address |
| cursor | query | no | Pagination cursor |
| limit | query | no | Page size (default 20, max 100) |

**Response example:**
```json
{
  "code": 200,
  "data": {
    "content": [
      {
        "contract_token_id": "0x0000000000000000000000000000000000000000000000000000000000000001",
        "owner": "0x2e97778b97db81b62eb64103813e019f353537cd",
        "token_id": "1"
      }
    ],
    "next": "NS871DD6359B64187E",
    "total": 30000
  },
  "msg": ""
}
```

---

#### Get owners by an NFT (ERC1155)

```
GET /api/v2/asset/owners/{chain_name}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| chain_name | path | yes | Chain to query |
| contract_address | query | yes | NFT contract address |
| token_id | query | yes | Token ID (Hex or Number) |
| cursor | query | no | Pagination cursor |
| limit | query | no | Page size (default 20, max 100) |

**Response example:**
```json
{
  "code": 200,
  "data": {
    "content": [
      {
        "account_address": "0xe525fae3fc6fbb23af05e54ff413613a6573cff2",
        "amount": "120"
      }
    ],
    "next": "NS871DD6359B64187E",
    "total": 30000
  },
  "msg": ""
}
```

---

#### Get latest block number

```
GET /api/v2/blocknumber/{chain_name}
```

**Response example:**
```json
{
  "code": 200,
  "data": {
    "block_number": 14839274
  },
  "msg": ""
}
```

---

#### Refresh NFT metadata

```
POST /api/v2/refresh/metadata/{chain_name}
```

**Request body:**
```json
{
  "contract_address": "0x3e855b7941fe8ef5f07dad68c5140d6a3ec1b286",
  "token_id": "0x000000000000000000000000000000000000000000000000000000000000013a"
}
```

**Response example:**
```json
{
  "code": 200,
  "data": {
    "reason": "task already exists",
    "status": "SUCCESS"
  },
  "msg": ""
}
```

---

#### Refresh NFT metadata by contract

Submits a background task to refresh metadata for an entire contract.

```
POST /api/v2/refresh/metadata/contract/{chain_name}
```

**Request body:**
```json
{
  "contract_address": "0x3e855b7941fe8ef5f07dad68c5140d6a3ec1b286"
}
```

---

## 2. Contracts API

Smart contract source code retrieval, ABI query, and verification services.

**Supported chains:** BSC Mainnet, BSC Testnet, opBNB Mainnet, opBNB Testnet

**Base URLs:**

| Chain | Endpoint |
|-------|----------|
| BSC Mainnet | `https://open-platform.nodereal.io/{apiKey}/bsc-mainnet/contract/` |
| BSC Testnet | `https://open-platform.nodereal.io/{apiKey}/bsc-testnet/contract/` |
| opBNB Mainnet | `https://open-platform.nodereal.io/{apiKey}/op-bnb-mainnet/contract/` |
| opBNB Testnet | `https://open-platform.nodereal.io/{apiKey}/op-bnb-testnet/contract/` |

---

### Get Contract Source Code for Verified Contracts

Returns the Solidity source code of a verified smart contract.

```
GET https://open-platform.nodereal.io/{apiKey}/bsc-mainnet/contract/?action=getsourcecode&address={contractAddress}
```

| Parameter | Required | Description |
|-----------|----------|-------------|
| address | yes | Contract address with verified source code |

**Response example:**
```json
{
  "status": "1",
  "message": "OK",
  "result": [
    {
      "SourceCode": "pragma solidity 0.6.12; ...",
      "ABI": "[{\"inputs\":[],\"stateMutability\":\"nonpayable\",\"type\":\"constructor\"}, ...]",
      "ContractName": "CakeToken",
      "CompilerVersion": "v0.6.12+commit.27d51765",
      "OptimizationUsed": "1",
      "Runs": "5000",
      "ConstructorArguments": "",
      "EVMVersion": "Default",
      "Library": "",
      "LicenseType": "None",
      "Proxy": "0",
      "Implementation": "",
      "SwarmSource": "ipfs://9755240809e31aec9fa5790314682233ca431b7c4f252d7e4bba347e2e742086"
    }
  ]
}
```

---

### Get Contract ABI for Verified Contracts

Returns the ABI for a verified smart contract.

```
GET https://open-platform.nodereal.io/{apiKey}/bsc-mainnet/contract/?action=getabi&address={contractAddress}
```

| Parameter | Required | Description |
|-----------|----------|-------------|
| address | yes | Contract address with verified source code |

**Response example:**
```json
{
  "status": "1",
  "message": "OK",
  "result": "[{\"inputs\":[],\"stateMutability\":\"nonpayable\",\"type\":\"constructor\"}, ...]"
}
```

**Usage with Web3.js:**
```javascript
var Web3 = require('web3');
var web3 = new Web3(new Web3.providers.HttpProvider());
$.getJSON(
  'https://open-platform.nodereal.io/{apiKey}/bsc-mainnet/contract/?action=getabi&address=0x55d398326f99059ff775485246999027b3197955',
  function (data) {
    var contractABI = JSON.parse(data.result);
    var MyContract = web3.eth.contract(contractABI);
    var myContractInstance = MyContract.at("0x55d398326f99059ff775485246999027b3197955");
    var result = myContractInstance.balanceOf("0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb");
  }
);
```

---

### Verify Source Code

Submits contract source code for verification. Returns a GUID receipt for tracking status.

```
POST https://open-platform.nodereal.io/{apiKey}/bsc-mainnet/contract?action=verifysourcecode
```

**Request parameters (form data):**

| Parameter | Required | Description |
|-----------|----------|-------------|
| contractaddress | yes | Contract address starting with 0x |
| sourceCode | yes | Contract source code (flattened if necessary) |
| codeformat | no | `solidity-single-file` (default) or `solidity-standard-json-input` |
| contractname | yes | Contract name |
| compilerversion | yes | Compiler version (e.g., `v0.8.19+commit.7dd6d404`) |
| optimizationUsed | no | `0` = No optimization, `1` = Optimization used |
| runs | no | Default 200 |
| constructorArguements | no | Constructor arguments if applicable |
| evmversion | no | EVM version (homestead, byzantium, istanbul, etc.) |
| licenseType | no | License code 1-12 (1=No License, 12=Apache 2.0) |

**Limits:**
- 100 submissions per day per user
- Only POST supported (HTTP GET size limitations)
- Supports up to 10 library pairs
- Only solc v0.4.11 and above

### Check Source Code Verification Status

```
GET https://open-platform.nodereal.io/{apiKey}/bsc-mainnet/contract?action=checkverifystatus&guid={guid}
```

**Response:**
```json
{"status": "1", "message": "OK", "result": "Pass - Verified"}
```

---

### Verify Proxy Contract

Submits a proxy contract for verification.

```
POST https://open-platform.nodereal.io/{apiKey}/bsc-mainnet/contract?action=verifyproxycontract
```

**Request parameters (form data):**

| Parameter | Required | Description |
|-----------|----------|-------------|
| address | yes | Proxy contract address |
| expectedimplementation | no | Expected implementation contract address |

**Response example:**
```json
{"status": "1", "message": "OK", "result": "gwgrrnfy56zf6vc1fljuejwg6pelnc5yns6fg6y2i6zfpgzquz"}
```

### Check Proxy Verification Status

```
GET https://open-platform.nodereal.io/{apiKey}/bsc-mainnet/contract?action=checkproxyverification&guid={guid}
```

**Response:**
```json
{
  "status": "1",
  "message": "OK",
  "result": "The proxy's (0xbc46363a7669f6e12353fa95bb067aead3675c29) implementation contract is found at 0xe45a5176bc0f2c1198e2451c4e4501d4ed9b65a6 and is successfully updated."
}
```

---

### Contracts API Summary

| Action | HTTP | Description |
|--------|------|-------------|
| `getsourcecode` | GET | Get verified contract source code |
| `getabi` | GET | Get verified contract ABI |
| `verifysourcecode` | POST | Submit source for verification |
| `checkverifystatus` | GET | Check verification status |
| `verifyproxycontract` | POST | Submit proxy contract for verification |
| `checkproxyverification` | GET | Check proxy verification status |

---

## 3. SPACE ID Universal Name Service API

Decentralized universal name service for BNB Chain `.bnb` domains.

**Base endpoint:**
```
https://open-platform.nodereal.io/{apiKey}/spaceid/domain
```

> **Note:** Use your BSC app API key. The API key is obtained from the MegaNode BSC app.

---

### byOwners -- Retrieve names by owner address

```
POST https://open-platform.nodereal.io/{apiKey}/spaceid/domain/names/byOwners
```

**Request body:**
```json
["0x159bc0357b89301dbfb110bee5e05c42c9db3798"]
```

**Response example:**
```json
{
  "0x159BC0357B89301dbFb110Bee5e05C42C9dB3798": [
    {
      "nodeHash": "0xdb99baace34eab2e2213adcc9940080fdb70bfaa15abdbf28fc61fca28da1b14",
      "bind": "0x159bc0357b89301dbfb110bee5e05c42c9db3798",
      "name": "defichad",
      "expires": "2023-08-24T20:36:26Z"
    },
    {
      "nodeHash": "0xb492a756f29f6030e974b3fda71b255bd20c61e23e3977321d4a895833631628",
      "bind": "0x159bc0357b89301dbfb110bee5e05c42c9db3798",
      "name": "michaelsaylor",
      "expires": "2023-08-24T20:35:59Z"
    }
  ]
}
```

---

### byBinds -- Retrieve names bound to an address

```
POST https://open-platform.nodereal.io/{apiKey}/spaceid/domain/names/byBinds
```

**Request body:**
```json
["0x159bc0357b89301dbfb110bee5e05c42c9db3798"]
```

**Response example:**
```json
{
  "0x159BC0357B89301dbFb110Bee5e05C42C9dB3798": [
    "defichad",
    "michaelsaylor",
    "win"
  ]
}
```

> **Note:** The owner can bind a name to a different address. `byOwners` returns names the address owns; `byBinds` returns names bound to (pointing at) the address.

---

### byNames -- Retrieve address by name

```
POST https://open-platform.nodereal.io/{apiKey}/spaceid/domain/binds/byNames
```

**Request body:**
```json
["win"]
```

**Response example:**
```json
{
  "win": {
    "nodeHash": "0x5431389acdea9db45b4d758954f139eb4d947c4e6293843b51ea0dbcd720620a",
    "bind": "0x159bc0357b89301dbfb110bee5e05c42c9db3798",
    "name": "win",
    "expires": "2023-08-24T20:38:29Z"
  }
}
```

> **Note:** Domain names are passed without the `.bnb` suffix. Add `.bnb` to get the full domain name.

---

## 4. Greenfield Enhanced API

Enhanced API methods for BNB Greenfield decentralized storage network.

**Base endpoints:**

| Network | Endpoint |
|---------|----------|
| Mainnet | `https://open-platform.nodereal.io/{apiKey}/greenfieldscan-mainnet/` |
| Testnet | `https://open-platform.nodereal.io/{apiKey}/greenfieldscan-testnet/` |

---

### Get transaction list by address

```
GET /greenfield/tx/list/by_address/{address}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| address | path | yes | Account address |
| page | query | yes | Page number |
| per_page | query | yes | Items per page |
| max_height | query | no | Maximum block height |
| tx_type | query | no | Transaction type filter |

**Response example:**
```json
[
  {
    "hash": [0],
    "height": 0,
    "index": 0,
    "time": "string",
    "tx_result": {
      "code": 0,
      "codespace": "string",
      "fee": "string",
      "gas_used": 0,
      "gas_wanted": 0,
      "module": "string",
      "type": "string"
    }
  }
]
```

---

### Get policy list by resource ID

```
GET /greenfield/permission/policy/list/by_resource/{id}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| id | path | yes | Resource ID |
| page | query | yes | Page number |
| per_page | query | yes | Items per page |

**Response:** Array of policy objects with `actions`, `policyID`, `principalType`, `resourceType`, `effect`, `expirationTime`, `removed`.

---

### Get daily bucket list

Returns daily bucket total charged storage size. Updated every few hours.

```
GET /greenfield/chart/daily_bucket/list
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| page | query | yes | Page number |
| per_page | query | yes | Items per page |
| owner | query | no | Filter by owner address |

**Response example:**
```json
[
  {
    "bucketID": [0],
    "bucketName": "string",
    "bucketNumID": "string",
    "dailyTotalChargedStorageSize": ["string"],
    "owner": [0]
  }
]
```

---

## 5. Greenfield Billing API

Billing and quota tracking for BNB Greenfield storage operations. Data updated daily at UTC 00:00.

**Base endpoints:**

| Network | Endpoint |
|---------|----------|
| Mainnet | `https://open-platform.nodereal.io/{apiKey}/greenfieldbilling-mainnet/` |
| Testnet | `https://open-platform.nodereal.io/{apiKey}/greenfieldbilling-testnet/` |

---

### Get total cost by owner address

```
GET /greenfield/total_cost/list/by_owner/{owner}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| owner | path | yes | Owner address |

**Response example:**
```json
[
  {
    "Cost": "string",
    "address": [0]
  }
]
```

---

### Get total cost by payment address

```
GET /greenfield/total_cost/list/by_payment/{payment}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| payment | path | yes | Payment address |

---

### Get monthly bill by owner address

```
GET /greenfield/bill_monthly/list/by_owner/{owner}
```

| Parameter | Location | Required | Description |
|-----------|----------|----------|-------------|
| owner | path | yes | Owner address |
| start_month | query | no | Start month |
| start_year | query | no | Start year |
| end_month | query | no | End month |
| end_year | query | no | End year |

**Response example:**
```json
[
  {
    "bills": [
      {
        "ReadCost": "string",
        "StoreCost": "string",
        "TotalCost": "string",
        "address": [0],
        "month": 0,
        "year": 0
      }
    ]
  }
]
```

---

### Get monthly bill by payment address

```
GET /greenfield/bill_monthly/list/by_payment/{payment}
```

Same parameters and response structure as the owner variant.

---

### Get real time bill by owner address

```
GET /greenfield/bill_realtime/list/by_owner/{owner}
```

### Get real time bill count by owner address

```
GET /greenfield/bill_realtime/count/by_owner/{owner}
```

### Get real time bill by payment address

```
GET /greenfield/bill_realtime/list/by_payment/{payment}
```

### Get real time bill count by payment address

```
GET /greenfield/bill_realtime/count/by_payment/{payment}
```

---

### Greenfield Billing API Summary

| Method | HTTP | Description |
|--------|------|-------------|
| `/greenfield/total_cost/list/by_owner/{owner}` | GET | Total cost by owner |
| `/greenfield/total_cost/list/by_payment/{payment}` | GET | Total cost by payment address |
| `/greenfield/bill_monthly/list/by_owner/{owner}` | GET | Monthly bill by owner |
| `/greenfield/bill_monthly/list/by_payment/{payment}` | GET | Monthly bill by payment address |
| `/greenfield/bill_realtime/list/by_owner/{owner}` | GET | Real-time bill by owner |
| `/greenfield/bill_realtime/count/by_owner/{owner}` | GET | Real-time bill count by owner |
| `/greenfield/bill_realtime/list/by_payment/{payment}` | GET | Real-time bill by payment address |
| `/greenfield/bill_realtime/count/by_payment/{payment}` | GET | Real-time bill count by payment address |

---

## 6. Covalent API

Unified blockchain data across multiple chains.

- Token balances (all tokens for an address in one call)
- Transaction history with decoded events
- Cross-chain data aggregation

---

## 7. Arbitrum Nova

**Endpoint:** `https://open-platform.nodereal.io/{apiKey}/arbitrum-nova/`

EVM-compatible chain. Supports standard Ethereum JSON-RPC methods (eth_call, eth_getBalance, eth_blockNumber, etc.). Optimized for high-throughput, low-cost transactions using AnyTrust technology.

**Supported Networks:** Arbitrum Nova (mainnet)

---

## 8. Avalanche Contract Chain (C-Chain)

**Endpoint:** `https://open-platform.nodereal.io/{apiKey}/avalanche-c/ext/bc/C/rpc`

EVM-compatible chain. Supports standard Ethereum JSON-RPC methods. Also supports Avalanche-specific AVAX methods -- see [non-evm-chains-reference.md](non-evm-chains-reference.md) for `avax.*` methods.

**Supported Networks:** Avalanche C-Chain (mainnet)

---

## 9. Arbitrum Nitro

**Endpoint:** `https://open-platform.nodereal.io/{apiKey}/arbitrum-nitro/`

EVM-compatible L2 chain. Supports standard Ethereum JSON-RPC methods plus debug_traceTransaction and debug_traceCall with Nitro-specific response formats.

**Supported Networks:** Arbitrum One (mainnet)

---

## 10. NEAR RPC

**Endpoint:** `https://open-platform.nodereal.io/{apiKey}/near/`

NEAR Protocol RPC access. See [non-evm-chains-reference.md](non-evm-chains-reference.md) for full NEAR method documentation including `query`, `block`, `chunk`, `tx`, and network methods.

---

## 11. Klaytn RPC

Klaytn (KAIA) blockchain JSON-RPC access with `klay_` namespace methods.

**Endpoint:**
```
https://open-platform.nodereal.io/{apiKey}/klaytn/
```

**Supported Networks:** Klaytn Cypress (mainnet)

### Account Methods (12)

| Method | Description |
|--------|-------------|
| `klay_accountCreated` | Check if account exists at address |
| `klay_accounts` | List addresses owned by client |
| `klay_getAccount` | Get account information (EOA or Smart Contract) |
| `klay_getAccountKey` | Get account key of EOA |
| `klay_encodeAccountKey` | RLP encode an account key |
| `klay_decodeAccountKey` | Decode RLP-encoded account key |
| `klay_getBalance` | Get balance in peb |
| `klay_getCode` | Get contract code at address |
| `klay_getTransactionCount` | Get nonce for address |
| `klay_isContractAccount` | Check if address is a contract |
| `klay_sign` | Sign data with address |
| `klay_getStorageAt` | Get storage value at position |

#### klay_getAccount

Returns account information. Klaytn has two account types: EOA (accType=1) and Smart Contract (accType=2).

```bash
curl https://open-platform.nodereal.io/{apiKey}/klaytn/ \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"klay_getAccount","params":["0x3111a0577f322e8fb54f78d9982a26ae7ca0f722", "latest"],"id":1}'
```

**Response (EOA):**
```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "accType": 1,
    "account": {
      "balance": 4985316100000000000,
      "humanReadable": false,
      "key": {
        "key": {
          "x": "0x230037a99462acd829f317d0ce5c8e2321ac2951de1c1b1a18f9af5cff66f0d7",
          "y": "0x18a7fb1b9012d2ac87bc291cbf1b3b2339356f1ce7669ae68405389be7f8b3b6"
        },
        "keyType": 2
      },
      "nonce": 11
    }
  }
}
```

#### klay_getBalance

```bash
curl https://open-platform.nodereal.io/{apiKey}/klaytn/ \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"klay_getBalance","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f", "latest"],"id":1}'
```

### Block Methods (11)

| Method | Description |
|--------|-------------|
| `klay_blockNumber` | Get current block number |
| `klay_getBlockByNumber` | Get block by number |
| `klay_getBlockByHash` | Get block by hash |
| `klay_getBlockReceipts` | Get all receipts in a block |
| `klay_getBlockTransactionCountByNumber` | Get tx count in block by number |
| `klay_getBlockTransactionCountByHash` | Get tx count in block by hash |
| `klay_getBlockWithConsensusInfoByHash` | Get block with consensus info by hash |
| `klay_getBlockWithConsensusInfoByNumber` | Get block with consensus info by number |
| `klay_getHeaderByHash` | Get header by hash |
| `klay_getHeaderByNumber` | Get header by number |
| `klay_getRewards` | Get block rewards |

### Transaction Methods (12)

| Method | Description |
|--------|-------------|
| `klay_getTransactionByHash` | Get transaction by hash |
| `klay_getTransactionByBlockHashAndIndex` | Get tx by block hash and index |
| `klay_getTransactionByBlockNumberAndIndex` | Get tx by block number and index |
| `klay_getTransactionBySenderTxHash` | Get tx by sender tx hash (Klaytn-specific) |
| `klay_getTransactionReceipt` | Get transaction receipt |
| `klay_getTransactionReceiptBySenderTxHash` | Get receipt by sender tx hash |
| `klay_sendRawTransaction` | Send signed transaction |
| `klay_sendTransaction` | Send transaction (node signs) |
| `klay_sendTransactionAsFeePayer` | Send as fee payer (fee delegation) |
| `klay_signTransaction` | Sign transaction without sending |
| `klay_signTransactionAsFeePayer` | Sign as fee payer |

Klaytn transactions include fee delegation fields: `feePayer`, `feePayerSignatures`, `feeRatio`, `senderTxHash`.

#### klay_getTransactionReceipt

```bash
curl https://open-platform.nodereal.io/{apiKey}/klaytn/ \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"klay_getTransactionReceipt","params":["0xaca5d9a1ed8b86b1ef61431b2bedfc99a66eaefc3a7e1cffdf9ff53653956a67"],"id":1}'
```

### Call / Execution Methods (3)

| Method | Description |
|--------|-------------|
| `klay_call` | Execute call without creating transaction |
| `klay_estimateGas` | Estimate gas for transaction |
| `klay_estimateComputationCost` | Estimate computation cost (Klaytn-specific) |

#### klay_call

```bash
curl https://open-platform.nodereal.io/{apiKey}/klaytn/ \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"jsonrpc": "2.0", "method": "klay_call", "params": [{"from": "0x3f71029af4e252b25b9ab999f77182f0cd3bc085", "to": "0x87ac99835e67168d4f9a40580f8f5c33550ba88b", "gas": "0x100000", "gasPrice": "0x5d21dba00", "value": "0x0", "input": "0x8ada066e"}, "latest"], "id": 1}'
```

### Gas / Fee Methods (4)

| Method | Description |
|--------|-------------|
| `klay_gasPrice` | Get current gas price in peb |
| `klay_gasPriceAt` | Get gas price at specific block |
| `klay_feeHistory` | Get fee history for block range |
| `klay_maxPriorityFeePerGas` | Get suggested gas tip cap |

### Filter Methods (2)

| Method | Description |
|--------|-------------|
| `klay_getFilterChanges` | Poll filter for new changes |
| `klay_getFilterLogs` | Get all logs matching filter |

### Chain Information Methods (7)

| Method | Description |
|--------|-------------|
| `klay_chainID` | Get chain ID (0x2019 = 8217 for Cypress) |
| `klay_clientVersion` | Get client version |
| `klay_protocolVersion` | Get protocol version |
| `klay_syncing` | Get sync status |
| `klay_rewardbase` | Get rewardbase address |
| `klay_isParallelDBWrite` | Check parallel DB write status |
| `klay_isSenderTxHashIndexingEnabled` | Check sender tx hash indexing |

### Council / Committee Methods (4)

| Method | Description |
|--------|-------------|
| `klay_getCouncil` | Get list of council validators |
| `klay_getCouncilSize` | Get council size |
| `klay_getCommittee` | Get list of committee validators |
| `klay_getCommitteeSize` | Get committee size |

### Utility (1)

| Method | Description |
|--------|-------------|
| `klay_sha3` | Compute Keccak-256 hash |

**Total: 54 klay_* methods**

### Klaytn Transaction Error Codes (txError)

| Code | Description |
|------|-------------|
| 0x02 | VM error while running smart contract |
| 0x03 | Max call depth exceeded |
| 0x07 | Out of gas |
| 0x09 | EVM: execution reverted |
| 0x0a | Opcode computation cost limit reached |
| 0x0e | Fee ratio out of range [1, 99] |

**Notes:**
- Klaytn uses **peb** (1 KLAY = 10^18 peb) and **ston** (1 ston = 10^9 peb)
- Fee delegation allows a separate fee payer via `feePayer` and `feeRatio` fields
- Block time is approximately 1 second
- `senderTxHash` differs from `transactionHash` for fee-delegated transactions

---

## 12. zkSync RPC

zkSync Era (zkEVM L2) with both standard Ethereum JSON-RPC and zkSync-exclusive methods.

**Endpoint:**
```
https://open-platform.nodereal.io/{apiKey}/zksync/
```

**Supported Networks:** zkSync Era (mainnet)

### Ethereum JSON-RPC Methods

Standard eth_* methods are supported (eth_call, eth_getBalance, eth_blockNumber, eth_getTransactionByHash, etc.).

### zkSync Exclusive Methods (zks_*)

| Method | Description |
|--------|-------------|
| `zks_estimateFee` | Estimate fee for a transaction |
| `zks_estimateGasL1ToL2` | Estimate gas for L1 to L2 transaction |
| `zks_getAllAccountBalances` | Get all token balances for an account |
| `zks_getBlockDetails` | Get zkSync-specific block details (L1 batch, commit/prove/execute hashes) |
| `zks_getBridgeContracts` | Get L1/L2 default bridge addresses |
| `zks_getBytecodeByHash` | Get bytecode by hash |
| `zks_getConfirmedTokens` | Get token info within a range |
| `zks_getL1BatchBlockRange` | Get block range in an L1 batch |
| `zks_getL1BatchDetails` | Get L1 batch details |
| `zks_getL2ToL1LogProof` | Get proof for L2 to L1 log |
| `zks_getL2ToL1MsgProof` | Get proof for L2 to L1 message |
| `zks_getMainContract` | Get zkSync Era contract address |
| `zks_getRawBlockTransactions` | Get raw transaction data in a block |
| `zks_getTestnetPaymaster` | Get testnet paymaster address |
| `zks_getTokenPrice` | Get token price in USD |
| `zks_getTransactionDetails` | Get zkSync-specific tx details (commit/prove/execute hashes) |
| `zks_L1BatchNumber` | Get latest L1 batch number |
| `zks_L1ChainId` | Get underlying L1 chain ID |

#### zks_estimateFee

```bash
curl https://open-platform.nodereal.io/{apiKey}/zksync/ \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"method":"zks_estimateFee","params":[{"from":"0x1111111111111111111111111111111111111111","to":"0x2222222222222222222222222222222222222222","data":"0xffffffff"}],"id":1,"jsonrpc":"2.0"}'
```

**Returns:** `gas_limit`, `gas_per_pubdata_limit`, `max_fee_per_gas`, `max_priority_fee_per_gas`

#### zks_getBlockDetails

```bash
curl https://open-platform.nodereal.io/{apiKey}/zksync/ \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"method":"zks_getBlockDetails","params":[140599],"id":1,"jsonrpc":"2.0"}'
```

#### zks_getAllAccountBalances

```bash
curl https://open-platform.nodereal.io/{apiKey}/zksync/ \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"method":"zks_getAllAccountBalances","params":["0x98E9D288743839e96A8005a6B51C770Bbf7788C0"],"id":1,"jsonrpc":"2.0"}'
```

---

## 13. BASE RPC

**Endpoint:** `https://open-platform.nodereal.io/{apiKey}/base/`

EVM-compatible L2 chain (Coinbase). Supports standard Ethereum JSON-RPC methods.

**Supported Networks:** BASE (mainnet only)

```bash
curl https://open-platform.nodereal.io/{apiKey}/base/ \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"method":"eth_blockNumber","params":[],"id":1,"jsonrpc":"2.0"}'
```

---

## 14. COMBO RPC

**Endpoints:**
- Mainnet: `https://combo-mainnet.nodereal.io/v1/{apiKey}`
- Testnet: `https://combo-testnet.nodereal.io/v1/{apiKey}`

EVM-compatible chain. Supports standard Ethereum JSON-RPC methods.

**Supported Networks:** COMBO (mainnet & testnet)

```bash
curl https://combo-mainnet.nodereal.io/v1/{apiKey} \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"method":"eth_blockNumber","params":[],"id":1,"jsonrpc":"2.0"}'
```

---

## 15. Particle Bundler RPC

Account Abstraction (ERC-4337) bundler service.

| Method | Description |
|--------|-------------|
| `eth_sendUserOperation` | Submit user operation |
| `eth_estimateUserOperationGas` | Gas estimation |
| `eth_getUserOperationByHash` | Query operation status |
| `eth_supportedEntryPoints` | Get supported entry points |

---

## 16. BNB Chain Staking API

**Endpoint:** `https://open-platform.nodereal.io/{apiKey}/bnb-chain-staking/`

**Supported Networks:** BSC only

### Get daily staking rewards by delegator

```
GET /bnb-chain-staking/bnb-staking-enhance/staking/reward/list/by_delegator/{address}
```

| Parameter | Location | Required | Type | Description |
|-----------|----------|----------|------|-------------|
| address | path | yes | string | Delegator address |
| start | query | yes | integer | Start timestamp |
| end | query | yes | integer | End timestamp |

**Response fields:** `delegator`, `date`, `operator` (validator), `change` (rewards), `balance`, `apr` (Reward / (Balance - Reward) * 365), `type`, `number`

**Example:**
```
https://open-platform.nodereal.io/{apiKey}/bnb-chain-staking/bnb-staking-enhance/staking/reward/list/by_delegator/0xF2B1d86DC7459887B1f7Ce8d840db1D87613Ce7f?start=1727827200&end=1728259200
```

---

## 17. PancakeSwap GraphQL v2

DEX data queries for PancakeSwap (Premium tier). Supports pair data, token statistics, transaction history, and swap events.

---

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| `getsourcecode` returns empty | Contract is not verified | Only verified contracts return source code; verify first |
| Marketplace API returns 403 | API not activated in dashboard | Activate the API in MegaNode dashboard under Marketplace |
| NFTScan returns empty results | Wrong chain or address format | Verify `chain_name` parameter and address checksum |
| Contracts API wrong chain | Incorrect chain-network identifier | Use `bsc-mainnet`, `bsc-testnet`, `op-bnb-mainnet`, or `op-bnb-testnet` |
| Greenfield API errors | API requires specific activation | Activate Greenfield APIs separately in the dashboard |
| `verifysourcecode` fails | Compiler version mismatch | Match exact compiler version and optimization settings from deployment |
| SPACE ID returns empty | Name not registered or expired | Verify the name is registered on SPACE ID and has not expired |
| NFTScan pagination stops | Cursor expired or invalid | Re-fetch from the beginning; cursors may expire |

---

## Documentation Links

- **NodeReal Marketplace:** https://nodereal.io/marketplace
- **NFTScan API:** https://docs.nodereal.io/reference/nftscan-api
- **Contracts API:** https://docs.nodereal.io/reference/get-contract-source-code-for-verified-contract-source-codes
- **SPACE ID API:** https://docs.nodereal.io/reference/space-id-universal-name-service-api
- **Greenfield Enhanced API:** https://docs.nodereal.io/reference/greenfield-enhanced-api
- **Greenfield Billing API:** https://docs.nodereal.io/reference/greenfield-billing-api
- **API Reference:** https://docs.nodereal.io/reference
