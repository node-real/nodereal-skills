# MegaNode Skills

A collection of AI agent skills for blockchain development powered by [NodeReal MegaNode](https://nodereal.io/meganode).

## Overview

MegaNode Skills helps AI agents (like Claude Code) understand and use NodeReal's MegaNode blockchain infrastructure APIs effectively. It provides structured knowledge about RPC endpoints, Enhanced APIs, gasless transactions, MEV protection, and more across 25+ supported blockchains.

## Installation

```bash
claude skill install /path/to/meganode-skills
```

## Project Structure

```
meganode-skills/
├── .claude-plugin/
│   └── marketplace.json                  # Plugin manifest
├── skills/
│   └── meganode-skill/
│       ├── SKILL.md                      # Main skill definition (11 products)
│       └── references/
│           ├── rpc-reference.md          # JSON-RPC API reference
│           ├── enhanced-api-reference.md # Enhanced API (nr_/ng_ methods)
│           ├── megafuel-reference.md     # Gasless transaction API
│           ├── direct-route-reference.md # MEV protection
│           ├── websocket-reference.md    # WebSocket subscriptions
│           ├── pricing-reference.md      # Plans, CUs, rate limits
│           ├── supported-chains.md       # Chain support matrix
│           ├── beacon-chain-reference.md # ETH Beacon Chain (consensus layer)
│           ├── portal-api-reference.md   # Account & CU usage management
│           ├── marketplace-reference.md  # Third-party API marketplace
│           ├── non-evm-chains-reference.md # Aptos, NEAR, BTC, TRON, Sui, Cosmos
│           └── jwt-authentication-reference.md # JWT token auth
├── LICENSE.md
└── README.md
```

## Supported Products

| Product | Description |
|---------|-------------|
| **MegaNode RPC** | JSON-RPC endpoints for 25+ blockchains |
| **Enhanced APIs** | Token (ERC-20) and NFT (ERC-721/1155) data queries |
| **MegaFuel** | Gasless transactions via BEP-322 paymaster |
| **Direct Route** | MEV protection via NodeReal Builder |
| **WebSocket** | Real-time blockchain event subscriptions |
| **Debug/Trace** | Transaction tracing and debugging APIs |
| **ETH Beacon Chain** | Consensus layer validator and staking data |
| **Portal API** | CU consumption monitoring and usage analytics |
| **API Marketplace** | NFTScan, SPACE ID, Contracts API, PancakeSwap, etc. |
| **Non-EVM Chains** | Aptos, NEAR, Bitcoin, TRON, Sui, Cosmos |
| **JWT Authentication** | Secure token-based auth for production |

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## License

MIT License - see [LICENSE.md](LICENSE.md) for details.
