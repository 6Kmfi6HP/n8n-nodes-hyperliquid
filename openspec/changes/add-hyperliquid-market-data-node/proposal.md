## Why
Hyperliquid users rely on n8n to orchestrate monitoring and trading flows. Today the package lacks any nodes that surface Hyperliquid market data, forcing builders to call HTTP endpoints manually. Adding a first-class market data node removes boilerplate and establishes the foundation for deeper automation work.

## What Changes
- Add a declarative `Hyperliquid Market Data` node under `nodes/Hyperliquid/`
- Expose operations to fetch tickers, order book snapshots, and recent trades via the public REST API
- Provide dynamic dropdowns for market symbols using Hyperliquid listings
- Handle API errors and rate-limit responses with clear messaging in node execution
- Document authentication expectations (no credentials required) and usage examples in README updates

## Impact
- Enables workflows that react to Hyperliquid price movements without custom HTTP Request nodes
- Establishes the Hyperliquid integration namespace for future trading and account features
- Minimal deployment risk: read-only endpoints with public data
