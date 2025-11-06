## Context
Hyperliquid provides a TypeScript SDK (`@nktkas/hyperliquid`) that wraps REST and WebSocket APIs for market data, account queries, and trade execution. n8n nodes benefit from SDK integration to gain type safety, tested API patterns, and consistent error handling without reimplementing HTTP clients. The SDK requires `viem` for wallet and signing functionality, establishing it as a core dependency for authenticated operations.

### Current State
- Proposal 1 (market data node) uses direct HTTP calls for public endpoints
- No SDK integration exists; future nodes would duplicate HTTP logic
- Credential system (Proposal 3) will provide private key management but lacks SDK wiring

### Stakeholders
- Node developers: consume SDK clients in resource files
- End users: benefit from reliable API access and clear error messages
- Maintainers: reduce surface area for bugs by delegating protocol logic to SDK

## Goals / Non-Goals

### Goals
- Integrate `@nktkas/hyperliquid` SDK as the primary API client for all Hyperliquid nodes
- Provide a reusable factory function that nodes can call to obtain configured SDK clients
- Map SDK errors to n8n error types with actionable messages
- Support environment switching (mainnet/testnet) and credential-based authentication
- Enable connection pooling and performance optimization for repeated API calls

### Non-Goals
- Rewrite existing market data node to use SDK (can remain HTTP-based unless refactored later)
- Implement WebSocket subscriptions in this proposal (deferred to Proposal 11)
- Add custom API retry logic beyond SDK defaults (unless SDK proves insufficient)
- Support non-standard API endpoints or forks of Hyperliquid

## Decisions

### Decision: Use `@nktkas/hyperliquid` as the official SDK
**Rationale**: This is the community-maintained TypeScript SDK with active development, comprehensive API coverage, and viem integration. Alternatives like direct REST calls lack type safety and require manual error handling.

**Alternatives considered**:
1. Direct HTTP calls with `axios` or `fetch`
   - Pro: No external SDK dependency
   - Con: Requires reimplementing request signing, error parsing, and type definitions
   - Verdict: Rejected due to maintenance burden and code duplication
2. Build a custom SDK wrapper
   - Pro: Full control over implementation
   - Con: High development cost, testing burden, and long-term maintenance
   - Verdict: Rejected; prefer battle-tested community SDK

### Decision: Factory pattern for SDK client creation
**Rationale**: Nodes should call `createHyperliquidClient(context, options)` to obtain preconfigured SDK instances. This centralizes transport setup, credential wiring, and environment configuration.

**Implementation**:
```typescript
// shared/sdk.ts
export function createHyperliquidClient(
  context: IExecuteFunctions,
  options?: { environment?: 'mainnet' | 'testnet'; credentials?: ICredentialDataDecryptedObject }
): HyperliquidClient {
  // Configure transport, inject credentials, return client
}
```

**Alternatives considered**:
1. Singleton client shared across all nodes
   - Pro: Single connection pool
   - Con: Breaks isolation, complicates credential switching per node
   - Verdict: Rejected; nodes may use different credentials or environments
2. Direct SDK instantiation in each node
   - Pro: Maximum flexibility
   - Con: Duplicates transport and error handling logic
   - Verdict: Rejected; factory abstracts repetitive setup

### Decision: Use `viem` for wallet and signing
**Rationale**: The SDK depends on `viem` for Ethereum wallet functionality (Hyperliquid uses EVM-compatible addresses and signatures). Adopting `viem` aligns with SDK requirements and provides robust cryptographic primitives.

**Alternatives considered**:
1. Use `ethers.js` for wallet operations
   - Pro: More widely known in the community
   - Con: SDK explicitly requires `viem`; mixing libraries adds bundle size
   - Verdict: Rejected; stick with SDK's peer dependency

### Decision: Map SDK errors to `NodeOperationError` and `NodeApiError`
**Rationale**: n8n expects errors in specific formats for workflow execution logs and UI display. Converting SDK exceptions ensures consistent error reporting across all nodes.

**Error mapping strategy**:
- Network errors (timeouts, DNS failures) → `NodeApiError` with retry hint
- API errors (rate limits, invalid parameters) → `NodeOperationError` with parameter guidance
- SDK-specific errors (signing failures, invalid credentials) → `NodeOperationError` with credential check prompt

**Implementation**:
```typescript
// shared/errors.ts
export function convertSdkError(error: unknown, context: IExecuteFunctions): NodeApiError | NodeOperationError {
  // Inspect error type, extract message, map to n8n error class
}
```

### Decision: Lazy connection pooling per workflow execution
**Rationale**: Creating one SDK client per node execution within a workflow is acceptable; n8n workflows are short-lived. Future optimization can introduce execution-scoped caching if performance data warrants it.

**Trade-off**: Slight overhead for repeated API calls in large workflows, but simplifies state management and avoids connection leaks.

## Risks / Trade-offs

### Risk: SDK breaking changes in future versions
**Mitigation**: Pin `@nktkas/hyperliquid` to a specific minor version in `package.json`. Monitor SDK releases and test upgrades in testnet before updating production nodes.

### Risk: viem dependency size increases bundle size
**Mitigation**: Accept the trade-off; `viem` is required for signing operations and provides tree-shakeable modules. Bundle size impact is acceptable for a community node package.

### Risk: SDK may not support all Hyperliquid API endpoints
**Mitigation**: If SDK lacks coverage for specific endpoints, fallback to direct HTTP calls in individual nodes. Document which operations use SDK vs HTTP. Contribute missing features back to SDK if feasible.

### Trade-off: Factory pattern adds indirection
**Impact**: Node developers must call factory instead of instantiating SDK directly.
**Justification**: Centralized configuration outweighs the minor complexity; factory ensures consistent transport and error handling.

## Migration Plan

### Phase 1: Add dependencies and SDK integration (this proposal)
1. Update `package.json` with `@nktkas/hyperliquid` and `viem`
2. Implement `shared/sdk.ts` and `shared/errors.ts`
3. Document usage in README for node developers

### Phase 2: Adopt SDK in new nodes (Proposals 5-10)
- Account info nodes (Proposal 5) will be first consumers
- Trading nodes (Proposals 6-10) will use SDK for authenticated operations
- Market data node (Proposal 1) can remain HTTP-based unless refactored

### Rollback plan
If SDK integration proves problematic, remove `shared/sdk.ts` and revert `package.json` changes. Nodes can fallback to direct HTTP calls without workflow disruption.

## Open Questions
1. Should we wrap SDK methods in n8n-specific interfaces to allow future SDK swaps?
   - **Answer**: No, prefer direct SDK usage for simplicity. Abstraction can be added later if multiple SDKs emerge.
2. How should we handle SDK rate limiting internally?
   - **Answer**: Rely on SDK defaults initially. If insufficient, add configurable retry with exponential backoff in `shared/sdk.ts`.
3. Should WebSocket transport be implemented now or deferred?
   - **Answer**: Deferred to Proposal 11 (WebSocket subscriptions). HTTP transport is sufficient for REST API operations.
