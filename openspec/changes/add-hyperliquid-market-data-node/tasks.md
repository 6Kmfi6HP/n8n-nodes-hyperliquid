## 1. Project Setup
- [ ] 1.1 Create directory structure: `nodes/Hyperliquid/`
- [ ] 1.2 Create subdirectories: `resources/`, `shared/`, `listSearch/`
- [ ] 1.3 Add node icons (light/dark) to `icons/` directory

## 2. Core Node Implementation
- [ ] 2.1 Scaffold `nodes/Hyperliquid/MarketData.node.ts` with declarative configuration
- [ ] 2.2 Define node metadata (displayName, name, icon, group, version)
- [ ] 2.3 Configure requestDefaults with baseURL: `https://api.hyperliquid.xyz/info`
- [ ] 2.4 Add environment parameter (Mainnet/Testnet) with default to Mainnet
- [ ] 2.5 Implement Resource selection (Market Data)
- [ ] 2.6 Wire node into `package.json` n8n.nodes array

## 3. Resource Operations Implementation
- [ ] 3.1 Create `resources/marketData/index.ts` with operation definitions
- [ ] 3.2 Implement "Get All Mids" operation (allMids)
  - [ ] 3.2.1 Add operation option with routing configuration
  - [ ] 3.2.2 Configure POST request to `/info` with `{"type": "allMids"}`
  - [ ] 3.2.3 Add optional `dex` parameter for perp dex selection
  - [ ] 3.2.4 Parse response and return mids object
- [ ] 3.3 Implement "Get Order Book" operation (l2Book)
  - [ ] 3.3.1 Add operation option with routing configuration
  - [ ] 3.3.2 Add required `coin` parameter (symbol dropdown)
  - [ ] 3.3.3 Configure POST request with `{"type": "l2Book", "coin": "..."}`
  - [ ] 3.3.4 Parse levels array (bids/asks) from response
- [ ] 3.4 Implement "Get Recent Trades" operation (trades)
  - [ ] 3.4.1 Add operation option with routing configuration
  - [ ] 3.4.2 Add required `coin` parameter
  - [ ] 3.4.3 Add optional `n` parameter (number of trades, default: 20)
  - [ ] 3.4.4 Configure POST request with trade parameters
  - [ ] 3.4.5 Parse trade array with price, size, timestamp, side
- [ ] 3.5 Implement "Get Candle Snapshot" operation (candleSnapshot)
  - [ ] 3.5.1 Add operation option with routing configuration
  - [ ] 3.5.2 Add required `coin` parameter
  - [ ] 3.5.3 Add required `interval` parameter (1m, 5m, 15m, 1h, 1d, etc.)
  - [ ] 3.5.4 Add optional `startTime` and `endTime` parameters (epoch milliseconds)
  - [ ] 3.5.5 Configure POST request with candle parameters
  - [ ] 3.5.6 Parse OHLCV data from response
- [ ] 3.6 Implement "Get Market Metadata" operation (metaAndAssetCtxs)
  - [ ] 3.6.1 Add operation option with routing configuration
  - [ ] 3.6.2 Configure POST request with `{"type": "metaAndAssetCtxs"}`
  - [ ] 3.6.3 Parse universe, asset contexts, and metadata
- [ ] 3.7 Implement "Get Spot Metadata" operation (spotMeta)
  - [ ] 3.7.1 Add operation option with routing configuration
  - [ ] 3.7.2 Configure POST request with `{"type": "spotMeta"}`
  - [ ] 3.7.3 Parse tokens and universe arrays

## 4. Dynamic Options and Utilities
- [ ] 4.1 Create `listSearch/getSymbols.ts` for dynamic symbol dropdown
  - [ ] 4.1.1 Implement function to fetch symbols from metaAndAssetCtxs
  - [ ] 4.1.2 Support both perpetuals and spot symbols
  - [ ] 4.1.3 Cache results for performance
  - [ ] 4.1.4 Handle API errors gracefully
- [ ] 4.2 Create `listSearch/getIntervals.ts` for candle interval dropdown
  - [ ] 4.2.1 Provide standard intervals: 1m, 3m, 5m, 15m, 30m, 1h, 2h, 4h, 8h, 12h, 1d, 3d, 1w, 1M
- [ ] 4.3 Create `shared/transport.ts` for API request helper
  - [ ] 4.3.1 Implement `hyperliquidApiRequest` function
  - [ ] 4.3.2 Handle environment (Mainnet/Testnet) switching
  - [ ] 4.3.3 Add error handling and retry logic
- [ ] 4.4 Create `shared/utils.ts` for response parsing utilities
  - [ ] 4.4.1 Add functions to transform API responses to n8n-friendly format
  - [ ] 4.4.2 Handle number/string conversions for prices and sizes

## 5. Error Handling
- [ ] 5.1 Implement rate limit detection and user-friendly error messages
- [ ] 5.2 Handle network errors with retry suggestions
- [ ] 5.3 Validate required parameters before API calls
- [ ] 5.4 Provide helpful error messages for invalid symbols or parameters

## 6. Documentation and Testing
- [ ] 6.1 Update README with usage examples for each operation
- [ ] 6.2 Document authentication expectations (no credentials needed)
- [ ] 6.3 Add testing instructions for Mainnet and Testnet
- [ ] 6.4 Include example workflows for common use cases
- [ ] 6.5 Document response data structure for each operation

## 7. Code Quality
- [ ] 7.1 Run `npm run lint` and fix any issues
- [ ] 7.2 Run `npm run lint:fix` for auto-fixable issues
- [ ] 7.3 Run `npm run build` to ensure TypeScript compilation succeeds
- [ ] 7.4 Verify node appears correctly in n8n editor

## 8. Validation
- [ ] 8.1 Validate change with `openspec validate add-hyperliquid-market-data-node --strict`
- [ ] 8.2 Test each operation in n8n dev environment
- [ ] 8.3 Verify dynamic dropdowns work correctly
- [ ] 8.4 Test error handling with invalid inputs
- [ ] 8.5 Capture execution screenshots or logs for documentation (optional)
