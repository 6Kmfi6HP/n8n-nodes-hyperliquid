## ADDED Requirements

### Requirement: SDK Client Factory
The integration MUST provide a reusable factory function that creates configured Hyperliquid SDK clients for use in all nodes.

#### Scenario: Create unauthenticated SDK client
- **WHEN** a node calls `createHyperliquidClient(context)` without credentials
- **THEN** the factory MUST return an SDK client configured for public API access with appropriate transport and timeout settings

#### Scenario: Create authenticated SDK client
- **WHEN** a node calls `createHyperliquidClient(context, { credentials })` with valid Hyperliquid credentials
- **THEN** the factory MUST return an SDK client with signing capabilities enabled using the provided private key from the credential context

#### Scenario: Environment switching
- **WHEN** a node specifies `{ environment: 'testnet' }` in factory options
- **THEN** the SDK client MUST connect to Hyperliquid testnet endpoints instead of mainnet

#### Scenario: Connection reuse within workflow
- **WHEN** multiple nodes in the same workflow execution request SDK clients with identical configuration
- **THEN** the factory SHOULD reuse transport connections to minimize overhead and improve performance

### Requirement: HTTP Transport Configuration
The integration MUST configure HTTP transport with robust timeout and error handling for API communication.

#### Scenario: Configure HTTP timeout
- **WHEN** creating an SDK client
- **THEN** the HTTP transport MUST apply a default timeout of 30 seconds for API requests and allow override via factory options

#### Scenario: Handle network failures
- **WHEN** an API request encounters a network error (DNS failure, connection timeout, socket error)
- **THEN** the transport MUST surface the error with context indicating retry guidance and not crash the node execution

#### Scenario: API endpoint configuration
- **WHEN** the environment is set to mainnet or testnet
- **THEN** the transport MUST route requests to the correct Hyperliquid API base URL without manual endpoint configuration

### Requirement: Error Conversion
The integration MUST convert SDK errors into n8n-compatible error types with descriptive messages for workflow debugging.

#### Scenario: Map API errors to NodeOperationError
- **WHEN** the SDK throws an error due to invalid parameters or business logic failures
- **THEN** the error converter MUST wrap it in `NodeOperationError` with the original message and guidance on parameter correction

#### Scenario: Map network errors to NodeApiError
- **WHEN** the SDK throws a network-related error (timeout, unreachable host, HTTP 5xx)
- **THEN** the error converter MUST wrap it in `NodeApiError` with retry hints and status code information if available

#### Scenario: Preserve error context for debugging
- **WHEN** converting any SDK error
- **THEN** the converter MUST include the original error message, operation name, and relevant context (e.g., symbol, order ID) in the n8n error output

#### Scenario: Handle rate limit errors
- **WHEN** the Hyperliquid API responds with a rate limit error (HTTP 429)
- **THEN** the error converter MUST surface a clear message instructing the user to wait and retry, with the retry-after duration if provided by the API

### Requirement: Type Safety and Documentation
The SDK integration MUST expose type-safe interfaces and comprehensive documentation for node developers.

#### Scenario: Export SDK types
- **WHEN** a node imports from `shared/sdk.ts`
- **THEN** the module MUST export relevant SDK types (client interfaces, request/response types) for use in node resource files

#### Scenario: Document factory parameters
- **WHEN** a developer reads the `createHyperliquidClient` function signature
- **THEN** JSDoc comments MUST describe each parameter (context, options), expected credential structure, and return type

#### Scenario: TypeScript strict mode compliance
- **WHEN** the project is built with TypeScript strict mode enabled
- **THEN** `shared/sdk.ts` and `shared/errors.ts` MUST compile without type errors or unsafe casts
