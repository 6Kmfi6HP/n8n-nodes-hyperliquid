## Why
Hyperliquid workflows need comprehensive access to exchange metadata, historical data, and deeper market information. While Proposal 1 provides basic market snapshots (real-time order book snapshots and recent trades), users building advanced trading strategies require historical candlestick data, funding rate history, extended trade history, detailed asset contexts, and deeper order book analysis. Adding a dedicated Info Query node unlocks these analytics capabilities without forcing users to construct complex HTTP requests.

**Note on Functional Overlap**: This proposal complements Proposal 1 by focusing on historical and analytical queries. While both proposals use the same underlying API endpoints (`l2Book` and `candleSnapshot`), Proposal 1 provides real-time snapshots for immediate market monitoring, while Proposal 2 provides historical depth, extended time ranges, and analytical capabilities (e.g., funding rate history, asset contexts, user public info) that are not covered in Proposal 1.

## What Changes
- Add a declarative `Hyperliquid Info Query` node under `nodes/Hyperliquid/`
- Expose Info API endpoints for metadata, asset contexts, L2 order books (with depth configuration), historical candles, extended trades history, and funding rates
- Organize operations by resource type: Metadata, Order Book, Candles, Trades, Funding, and User Info
- Provide structured resources under `resources/info/` for maintainability
- Support pagination and filtering parameters for historical data queries with conservative default limits
- Handle API errors with descriptive messages and validation for required parameters
- Document query patterns and data formats in README updates

## Impact
- Affected specs: `hyperliquid-info` (new capability)
- Affected code:
  - `nodes/Hyperliquid/InfoQuery.node.ts` (new node implementation)
  - `resources/info/meta.ts` (metadata queries)
  - `resources/info/orderBook.ts` (L2 order book depth with configurable depth)
  - `resources/info/candles.ts` (historical candlestick data)
  - `resources/info/trades.ts` (historical trades with pagination)
  - `resources/info/funding.ts` (funding rate history)
  - `resources/info/user.ts` (public user information)
  - `shared/descriptions.ts` (updated with Info API descriptions)
  - `package.json` (node registration)
- **Reused infrastructure from Proposal 1**:
  - `shared/transport.ts` - API request helper and environment switching (Mainnet/Testnet)
  - `listSearch/getSymbols.ts` - Dynamic symbol dropdown implementation
  - `shared/utils.ts` - Response parsing utilities and error handling patterns
  - Environment parameter configuration pattern (Mainnet/Testnet)
- Dependencies: **Requires Proposal 1 to be approved and implemented first** to reuse shared infrastructure
- Minimal risk: All endpoints are read-only public queries requiring no authentication
