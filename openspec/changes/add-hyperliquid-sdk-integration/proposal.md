## Why
Hyperliquid workflows require reliable API access for market data, account queries, and trade execution. Direct HTTP calls add complexity, duplicate error handling, and lack type safety. Integrating the official `@nktkas/hyperliquid` SDK provides battle-tested API wrappers, type-safe interfaces, and consistent error handling patterns that streamline node implementation and reduce maintenance burden.

## What Changes
- Add `@nktkas/hyperliquid` and `viem` dependencies to `package.json`
- Create `shared/sdk.ts` factory module to instantiate SDK clients with transport layer configuration
- Implement `HttpTransport` and `WebSocketTransport` wrappers for API communication
- Add `shared/errors.ts` to convert SDK exceptions into n8n-compatible error messages
- Support connection pooling, timeout configuration, and mainnet/testnet environment switching
- Provide credential integration hooks for authenticated API calls (depends on Proposal 3)

## Impact
- Affected specs: `hyperliquid-sdk` (new), future trading and account nodes will build on this foundation
- Affected code: `package.json` (dependencies), `shared/sdk.ts` (new), `shared/errors.ts` (new)
- Breaking: None (foundational change)
- Future nodes: All authenticated Hyperliquid operations will consume the SDK client factory
