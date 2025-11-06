## 1. Dependencies
- [ ] 1.1 Add `@nktkas/hyperliquid` to `package.json` dependencies (latest stable version)
- [ ] 1.2 Add `viem` to `package.json` dependencies (peer dependency for SDK)
- [ ] 1.3 Run `npm install` and verify lockfile updates

## 2. SDK Client Factory
- [ ] 2.1 Create `shared/sdk.ts` with client factory function
- [ ] 2.2 Implement `createHyperliquidClient()` that accepts environment (mainnet/testnet) and optional credentials
- [ ] 2.3 Add transport configuration support (HTTP timeout, retry settings)
- [ ] 2.4 Support connection reuse across multiple node executions in same workflow

## 3. Transport Layer
- [ ] 3.1 Implement `HttpTransport` wrapper with timeout and error handling
- [ ] 3.2 Implement `WebSocketTransport` wrapper (if needed for future WebSocket nodes)
- [ ] 3.3 Add environment-specific endpoint configuration

## 4. Error Handling
- [ ] 4.1 Create `shared/errors.ts` with error conversion utilities
- [ ] 4.2 Map SDK exceptions to n8n `NodeOperationError` and `NodeApiError`
- [ ] 4.3 Add descriptive error messages for common failure modes (rate limits, network errors, invalid parameters)
- [ ] 4.4 Include retry guidance in error messages where applicable

## 5. Type Safety
- [ ] 5.1 Export SDK types from `shared/sdk.ts` for use in nodes
- [ ] 5.2 Add JSDoc comments documenting client factory parameters and return types
- [ ] 5.3 Ensure TypeScript strict mode compliance

## 6. Testing
- [ ] 6.1 Manually test SDK client creation with testnet configuration
- [ ] 6.2 Verify error handling for common failure scenarios
- [ ] 6.3 Confirm connection pooling and performance optimization

## 7. Validation
- [ ] 7.1 Run `npm run lint` and resolve any issues
- [ ] 7.2 Run `npm run build` to verify TypeScript compilation
- [ ] 7.3 Validate change with `openspec validate add-hyperliquid-sdk-integration --strict`
