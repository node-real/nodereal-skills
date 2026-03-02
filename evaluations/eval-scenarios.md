# NodeReal MegaNode Skill — Evaluation Scenarios

These scenarios test whether the skill provides accurate, complete guidance when a developer asks common questions. Each scenario specifies a user prompt, the expected behavior, verification criteria, and failure conditions.

---

## Scenario 1: Query BSC ERC-20 Token Balance

**Product:** Enhanced API
**Expected Reference:** `enhanced-api-reference.md`

**User Prompt:**
> How do I check the USDT balance of address 0xF977814e90dA44bFA03b6295A0616a897441aceC on BSC?

**Expected Behavior:**
1. Recommend `nr_getTokenBalance20` method
2. Provide correct endpoint: `https://bsc-mainnet.nodereal.io/v1/{apiKey}`
3. Show JSON-RPC request with params: `[blockNumber, contractAddress, accountAddress]`
4. Use USDT contract address `0x55d398326f99059fF775485246999027B3197955`

**Verification Criteria:**
- Method name is exactly `nr_getTokenBalance20` (not `eth_call` with ABI encoding)
- Endpoint domain is `bsc-mainnet.nodereal.io`
- Params array has 3 elements in correct order

**Failure Conditions:**
- Uses `ng_getTokenBalance20` (wrong prefix)
- Uses `open-platform.nodereal.io` endpoint for Enhanced API
- Omits block number parameter

---

## Scenario 2: Configure MegaFuel Gasless Transaction

**Product:** MegaFuel
**Expected Reference:** `megafuel-reference.md`

**User Prompt:**
> Set up gasless transactions for my dApp on BSC. How do I check if a transaction is sponsorable and then send it?

**Expected Behavior:**
1. Explain the MegaFuel sponsorship flow
2. Show `pm_isSponsorable` check first
3. Show `eth_sendRawTransaction` via MegaFuel endpoint for sending
4. Use correct endpoint: `https://open-platform-ap.nodereal.io/{apiKey}/bsc-mainnet`
5. Include safety warning about confirming transactions

**Verification Criteria:**
- Endpoint uses `open-platform-ap.nodereal.io` (not `open-platform.nodereal.io`)
- Flow is: check sponsorable → sign transaction → send via MegaFuel
- Safety warning present for transaction submission

**Failure Conditions:**
- Uses `open-platform.nodereal.io` without `-ap` suffix for MegaFuel
- Skips `pm_isSponsorable` check
- No safety/confirmation warning for sending transactions

---

## Scenario 3: Query NFT Holdings via NFTScan

**Product:** NFTScan (Marketplace)
**Expected Reference:** `nftscan-reference.md`

**User Prompt:**
> I want to get all NFTs owned by a specific wallet address on BSC using NodeReal's NFTScan API.

**Expected Behavior:**
1. Recommend the account ownership endpoint
2. Provide correct endpoint pattern: `https://open-platform.nodereal.io/{apiKey}/nftscan/api/v2/account/own/{address}?chain_name=bsc`
3. Explain pagination via `cursor` parameter
4. Mention that NFTScan must be activated in the MegaNode Marketplace dashboard

**Verification Criteria:**
- URL path includes `/nftscan/api/v2/account/own/`
- Uses `open-platform.nodereal.io` (not `-ap`)
- Includes `chain_name` query parameter
- Mentions dashboard activation requirement

**Failure Conditions:**
- Uses incorrect API path (e.g., `/namescan/` or `/nft/`)
- Uses MegaFuel `-ap` endpoint for NFTScan
- Omits `chain_name` parameter

---

## Scenario 4: Resolve .bnb Domain Name

**Product:** SPACE ID (Marketplace)
**Expected Reference:** `spaceid-reference.md`

**User Prompt:**
> What's the wallet address behind the domain win.bnb?

**Expected Behavior:**
1. Use SPACE ID API, specifically `POST /spaceid/domain/binds/byNames`
2. Correct endpoint: `https://open-platform.nodereal.io/{apiKey}/spaceid/domain/binds/byNames`
3. POST body: `["win"]` (without `.bnb` suffix)
4. Parse response to extract bound address

**Verification Criteria:**
- Uses POST method (not GET)
- Endpoint path is `/spaceid/domain/binds/byNames` (not `/namescan/resolve` or similar)
- Request body strips `.bnb` suffix from the domain name
- Uses `open-platform.nodereal.io`

**Failure Conditions:**
- Uses non-existent endpoint like `/namescan/resolve`
- Includes `.bnb` suffix in request body
- Uses GET instead of POST

---

## Scenario 5: Get Contract Source Code with Sourcify Fallback

**Product:** Contracts API (Marketplace)
**Expected Reference:** `contracts-api-reference.md`

**User Prompt:**
> Get the source code for contract 0x0E09FaBB73Bd3Ade0a17ECC321fD13a19e81cE82 (PancakeSwap CAKE token) on BSC.

**Expected Behavior:**
1. First attempt: BscTrace `getsourcecode` endpoint
2. URL: `https://open-platform.nodereal.io/{apiKey}/bsc-mainnet/contract/?action=getsourcecode&address=0x0E09FaBB73Bd3Ade0a17ECC321fD13a19e81cE82`
3. If empty result: fall back to Sourcify API
4. Sourcify URL: `https://sourcify.dev/server/files/56/{address}`
5. Report which source provided the result

**Verification Criteria:**
- Primary attempt uses NodeReal BscTrace endpoint
- Fallback uses Sourcify with correct chain ID (56 for BSC)
- Reports verification source attribution
- Uses `open-platform.nodereal.io` for primary endpoint

**Failure Conditions:**
- Only uses Sourcify without trying BscTrace first
- Uses wrong chain ID for Sourcify
- No source attribution in response

---

## Scenario 6: Trace a Transaction

**Product:** Debug/Trace API
**Expected Reference:** `debug-trace-reference.md`

**User Prompt:**
> I need to debug a failed transaction on BSC. The transaction hash is 0xabc123... How do I trace it?

**Expected Behavior:**
1. Recommend `debug_traceTransaction` method
2. Use correct endpoint: `https://bsc-mainnet.nodereal.io/v1/{apiKey}`
3. Show tracer config with `callTracer` option
4. Explain the trace output structure (type, from, to, value, gas, calls)

**Verification Criteria:**
- Method is `debug_traceTransaction` (not `trace_transaction`)
- Tracer config includes `{"tracer": "callTracer"}`
- Mentions Debug Pro requirement for advanced features
- Standard JSON-RPC format

**Failure Conditions:**
- Uses wrong method name
- Omits tracer configuration
- Uses Marketplace endpoint instead of RPC endpoint

---

## Scenario 7: Multi-Chain dApp Initialization

**Product:** RPC + Common Patterns
**Expected Reference:** `rpc-reference.md`, `common-patterns-reference.md`

**User Prompt:**
> I'm building a dApp that needs to support BSC, Ethereum, and opBNB. How do I set up RPC connections for all three chains?

**Expected Behavior:**
1. Provide endpoint URLs for each chain:
   - BSC: `https://bsc-mainnet.nodereal.io/v1/{apiKey}`
   - Ethereum: `https://eth-mainnet.nodereal.io/v1/{apiKey}`
   - opBNB: `https://opbnb-mainnet.nodereal.io/v1/{apiKey}`
2. Show multi-chain configuration pattern
3. Mention chain IDs: BSC=56, Ethereum=1, opBNB=204

**Verification Criteria:**
- All three endpoint URLs use consistent format
- Chain identifiers in URL path are correct (`bsc-mainnet`, `eth-mainnet`, `opbnb-mainnet`)
- Chain IDs are accurate

**Failure Conditions:**
- Wrong chain identifier in URL (e.g., `binance` instead of `bsc`)
- Mixed endpoint format (some with `/v1/`, some without)
- Wrong chain IDs

---

## Scenario 8: Klaytn klay_getAccount

**Product:** Klaytn RPC (Marketplace)
**Expected Reference:** `klaytn-reference.md`

**User Prompt:**
> How do I get account details on the Klaytn network using NodeReal?

**Expected Behavior:**
1. Recommend `klay_getAccount` method
2. Correct endpoint: `https://klaytn-mainnet.nodereal.io/v1/{apiKey}`
3. Show JSON-RPC request with params: `[address, blockNumber]`
4. Explain Klaytn-specific account types (EOA, SCA)

**Verification Criteria:**
- Method is `klay_getAccount` (Klaytn-specific, not `eth_getBalance`)
- Endpoint is `klaytn-mainnet.nodereal.io`
- Mentions Klaytn account type system

**Failure Conditions:**
- Uses Ethereum method instead of Klaytn-specific method
- Uses wrong endpoint domain
- No mention of Klaytn-specific features
