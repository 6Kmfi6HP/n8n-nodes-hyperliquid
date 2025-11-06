## 0. Prerequisites and Dependencies
- [ ] 0.1 Verify Proposal 1 (`add-hyperliquid-market-data-node`) is approved and implemented
- [ ] 0.2 Confirm availability of shared infrastructure from Proposal 1:
  - [ ] 0.2.1 `shared/transport.ts` exists and provides API request helper
  - [ ] 0.2.2 `listSearch/getSymbols.ts` exists and provides symbol dropdown functionality
  - [ ] 0.2.3 `shared/utils.ts` exists with response parsing utilities
- [ ] 0.3 Review Proposal 1 implementation to understand shared patterns and conventions

## 1. Resource Structure
- [ ] 1.1 Create `resources/info/meta.ts` for metadata and asset context queries
- [ ] 1.2 Create `resources/info/orderBook.ts` for L2 order book depth queries (with configurable depth)
- [ ] 1.3 Create `resources/info/candles.ts` for historical candlestick snapshot queries
- [ ] 1.4 Create `resources/info/trades.ts` for historical trades queries with pagination
- [ ] 1.5 Create `resources/info/funding.ts` for funding rate history queries
- [ ] 1.6 Create `resources/info/user.ts` for public user information queries

## 2. Node Implementation
- [ ] 2.1 Scaffold `nodes/Hyperliquid/InfoQuery.node.ts` with declarative routing
- [ ] 2.2 Configure resource selection with Metadata, Order Book, Candles, Trades, Funding, User operations
- [ ] 2.3 Add environment parameter (Mainnet/Testnet) with default to Mainnet, reusing pattern from Proposal 1
- [ ] 2.4 Configure requestDefaults with baseURL using environment parameter (reuse `shared/transport.ts`)
- [ ] 2.5 Add dynamic symbol dropdowns by reusing `listSearch/getSymbols.ts` from Proposal 1
- [ ] 2.6 Implement pagination support for historical data operations with conservative default limits
- [ ] 2.7 Add validation for required parameters (symbol, interval, dates)
- [ ] 2.8 Configure request/response transformations for each resource type

## 3. Shared Utilities
- [ ] 3.1 Add Info API operation descriptions to `shared/descriptions.ts`
- [ ] 3.2 Add type definitions for Info API responses in `shared/types.ts`
- [ ] 3.3 Create helper functions for date/interval formatting if needed
- [ ] 3.4 Reuse error handling utilities from Proposal 1's `shared/utils.ts` and extend for Info API error scenarios
- [ ] 3.5 Ensure symbol validation reuses Proposal 1's `listSearch/getSymbols.ts` implementation to maintain consistency

## 4. Integration
- [ ] 4.1 Register `Hyperliquid Info Query` node in `package.json` under `n8n.nodes`
- [ ] 4.2 Update main exports in node entry files
- [ ] 4.3 Verify no conflicts with existing Market Data node

## 5. Documentation
- [ ] 5.1 Update README with Info Query node usage examples
- [ ] 5.2 Document each operation type with parameter descriptions
- [ ] 5.3 Add notes on rate limits and pagination best practices
- [ ] 5.4 Include sample workflow snippets for common queries

## 6. Validation
- [ ] 6.1 Run `openspec validate add-hyperliquid-info-query-node --strict`
- [ ] 6.2 Run `npm run lint` and fix any issues
- [ ] 6.3 Run `npm run build` and verify successful compilation
- [ ] 6.4 Test each operation in local n8n instance with `npm run dev`
- [ ] 6.5 Verify error handling for invalid symbols, missing parameters, API failures
