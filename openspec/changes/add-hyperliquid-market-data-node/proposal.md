## Why
Hyperliquid users rely on n8n to orchestrate monitoring and trading flows. Today the package lacks any nodes that surface Hyperliquid market data, forcing builders to call HTTP endpoints manually. Adding a first-class market data node removes boilerplate and establishes the foundation for deeper automation work.

## What Changes
- Add a declarative `Hyperliquid Market Data` node under `nodes/Hyperliquid/`
- Expose operations to fetch market data via the public REST API (Info endpoint):
  - **All Mids**: Fetch mid-prices for all perpetuals and spot markets
  - **Ticker/BBO**: Get best bid/offer for specific symbols
  - **Order Book (L2)**: Retrieve Level 2 order book snapshots with configurable depth
  - **Recent Trades**: Fetch recent trade history for symbols
  - **Candle Snapshot**: Retrieve historical OHLCV candle data
  - **Market Metadata**: Get asset metadata, tick/lot sizes, and universe information
  - **Spot Metadata**: Retrieve spot market token details and trading pairs
- Provide dynamic dropdowns for market symbols using Hyperliquid listings (both perpetuals and spot)
- Support both Mainnet and Testnet environments via node parameter
- Handle API errors and rate-limit responses with clear messaging in node execution
- Implement proper response parsing and data transformation for n8n workflow consumption
- Document authentication expectations (no credentials required for public data) and usage examples in README updates

## Impact
- Enables workflows that react to Hyperliquid price movements without custom HTTP Request nodes
- Establishes the Hyperliquid integration namespace for future trading and account features
- Minimal deployment risk: read-only endpoints with public data
- Provides foundation for more advanced features requiring signed requests
- Supports both perpetual futures and spot market data queries
