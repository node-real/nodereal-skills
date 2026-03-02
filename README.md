# MegaNode Skills

> A collection of AI agent skills for blockchain development powered by [NodeReal MegaNode](https://nodereal.io/meganode).

## Introduction

MegaNode Skills helps AI agents (like Claude Code) understand and use NodeReal's MegaNode blockchain infrastructure APIs effectively. It provides structured knowledge about RPC endpoints, Enhanced APIs, gasless transactions, MEV protection, and more across 25+ supported blockchains.

## What are Skills?

Skills are structured knowledge files that enhance AI coding agents with domain-specific expertise. They follow the [Agent Skills](https://agentskills.io) open standard — a portable format that works across different AI tools. When you install a skill, the AI agent gains deep understanding of specific APIs, tools, and best practices without needing to search external documentation.

## Available Skills

| Skill | Description |
|-------|-------------|
| **meganode-skill** | NodeReal MegaNode blockchain infrastructure — RPC (25+ chains), Enhanced APIs, MegaFuel, Direct Route, Debug/Trace, Marketplace, and more |

## Installation

### Quick Install (Recommended)

```bash
npx skills add node-real/nodereal-skills
```

Install globally (available across all projects):

```bash
npx skills add node-real/nodereal-skills -g
```

### Manual Install

**Personal skill** (available across all projects):

```bash
git clone git@github.com:node-real/nodereal-skills.git
cp -r nodereal-skills/skills/* ~/.claude/skills/
```

**Project skill** (current project only):

```bash
git clone git@github.com:node-real/nodereal-skills.git
cp -r nodereal-skills/skills/* .claude/skills/
```

### Local Testing

```bash
git clone git@github.com:node-real/nodereal-skills.git
claude --plugin-dir ./nodereal-skills
```

## Usage

Once installed, just ask natural language questions. The skill activates automatically based on context:

```
"Get the ERC-20 token balance of 0x... on BSC mainnet"
"What NFTs does this wallet hold?"
"Get the verified contract source code for 0x... on BSC"
"Set up a MegaFuel gasless transaction sponsor policy"
"Trace this transaction to debug the revert"
"Monitor new blocks on Ethereum via WebSocket"
"Get the Klaytn account info for 0x... using klay_getAccount"
"How many compute units does eth_call cost?"
```

## MegaNode Integration

### RPC Endpoints

```javascript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider(
  `https://bsc-mainnet.nodereal.io/v1/${process.env.NODEREAL_API_KEY}`
);
```

### Enhanced APIs (Token & NFT Data)

```javascript
// Get all ERC-20 tokens held by a wallet
const response = await fetch(RPC_URL, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    jsonrpc: "2.0", id: 1,
    method: "nr_getTokenHoldings",
    params: ["0xWalletAddress", "0x1", "0x64"],
  }),
});
```

### Smart Contract Source Code

```bash
curl "https://open-platform.nodereal.io/${NODEREAL_API_KEY}/bsc-mainnet/contract/?action=getsourcecode&address=0xContractAddress"
```

## Skill Structure

```
nodereal-skills/
├── .claude-plugin/
│   └── marketplace.json                        # Plugin manifest
├── skills/
│   └── meganode-skill/
│       ├── SKILL.md                            # Main skill definition (11 products)
│       └── references/
│           ├── rpc-reference.md                # JSON-RPC API reference
│           ├── enhanced-api-reference.md       # Enhanced API (nr_ methods)
│           ├── megafuel-reference.md           # Gasless transaction API
│           ├── direct-route-reference.md       # MEV protection
│           ├── websocket-reference.md          # WebSocket subscriptions
│           ├── debug-trace-reference.md        # Debug, Debug Pro, Trace APIs
│           ├── beacon-chain-reference.md       # ETH Beacon Chain (consensus layer)
│           ├── portal-api-reference.md         # Account & CU usage management
│           ├── non-evm-chains-reference.md     # Aptos, NEAR, Avalanche C-Chain
│           ├── pricing-reference.md            # Plans, CUs, rate limits
│           ├── supported-chains.md             # Chain support matrix
│           ├── jwt-authentication-reference.md # JWT token auth
│           ├── common-patterns-reference.md    # Multi-chain setup, monitoring patterns
│           ├── nftscan-reference.md            # NFTScan NFT data API
│           ├── contracts-api-reference.md      # Smart contract source/ABI/verification
│           ├── spaceid-reference.md            # SPACE ID .bnb domain resolution
│           ├── greenfield-reference.md         # BNB Greenfield storage & billing
│           ├── klaytn-reference.md             # Klaytn (KAIA) RPC methods
│           ├── zksync-reference.md             # zkSync Era RPC methods
│           └── marketplace-extras-reference.md # Covalent, BASE, COMBO, BNB Staking, etc.
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
| **API Marketplace** | NFTScan, Contracts, Klaytn, zkSync, SPACE ID, Greenfield, BNB Staking, and more |
| **Non-EVM Chains** | Aptos, NEAR, Avalanche C-Chain |
| **JWT Authentication** | Secure token-based auth for production |

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

### Skill Guidelines

- Keep SKILL.md as the hub with overview-level content (< 500 lines, description < 1024 chars)
- Use reference files in `references/` for detailed API documentation
- Include working code examples in TypeScript/JavaScript
- Add Troubleshooting tables (Issue | Cause | Solution) to reference files
- Cross-reference between files using relative markdown links
- Follow the [Agent Skills](https://agentskills.io) open standard

## License

MIT License - see [LICENSE.md](LICENSE.md) for details.

---

Built with [NodeReal MegaNode](https://nodereal.io/meganode) — [Get Started](https://nodereal.io/meganode)
