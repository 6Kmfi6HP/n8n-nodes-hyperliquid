## ADDED Requirements

### Requirement: Query Exchange Metadata and Asset Contexts
The integration MUST allow workflows to retrieve exchange metadata and asset context information from the Hyperliquid Info API.

#### Scenario: Get exchange metadata with all asset contexts
- **WHEN** a workflow executes the `Hyperliquid Info Query` node with the `Get Metadata` operation
- **THEN** the node MUST call the Hyperliquid `metaAndAssetCtxs` endpoint and return universe metadata with complete asset contexts including mark prices, funding rates, and open interest

#### Scenario: Get specific asset contexts
- **WHEN** a workflow executes the node with the `Get Asset Context` operation for selected symbols
- **THEN** the node MUST call the `assetCtxs` endpoint with the specified coin list and return only the requested asset contexts

#### Scenario: Parse asset metadata
- **WHEN** the metadata response is received
- **THEN** the node output MUST include properly typed fields for asset names, price decimals, size decimals, and max leverage values

### Requirement: Query L2 Order Book Depth
The integration MUST provide deep order book queries with configurable depth for analytical purposes. This complements Proposal 1's real-time order book snapshot by offering depth configuration and deeper market analysis capabilities.

#### Scenario: Get L2 order book with custom depth
- **WHEN** a workflow executes the node with the `Get L2 Order Book` operation for a symbol
- **THEN** the node MUST call the `l2Book` endpoint with the specified symbol and return aggregated bids and asks with price levels and sizes

#### Scenario: Order book depth configuration
- **WHEN** configuring the L2 order book operation
- **THEN** the node MUST allow users to specify depth limit via node parameters with reasonable defaults (e.g., default depth of 20 levels, maximum configurable depth of 100 levels)

#### Scenario: Handle thin order books
- **WHEN** the API returns fewer levels than requested depth
- **THEN** the node MUST return available levels without error and indicate actual depth in metadata

### Requirement: Query Candlestick Data
The integration MUST enable retrieval of historical candlestick (OHLCV) data for technical analysis. This complements Proposal 1's real-time candle snapshot by providing extended historical time ranges and analytical capabilities.

#### Scenario: Get candlestick snapshot
- **WHEN** a workflow executes the node with the `Get Candles` operation for a symbol and interval
- **THEN** the node MUST call the `candleSnapshot` endpoint and return an array of candles with timestamp, open, high, low, close, and volume
- **AND** the operation MUST enforce a default limit of 100 candles per request to prevent excessive data loads
- **AND** the operation MUST support a maximum limit of 1000 candles per request with clear warnings about performance implications

#### Scenario: Interval selection
- **WHEN** configuring the candles operation
- **THEN** the node MUST provide interval options including 1m, 5m, 15m, 1h, 4h, 1d matching Hyperliquid supported intervals

#### Scenario: Time range filtering
- **WHEN** optional start and end time parameters are provided
- **THEN** the node MUST filter candles to the specified time range using proper timestamp formatting
- **AND** the node MUST validate that the time range does not exceed 30 days by default to prevent excessive data retrieval
- **AND** the node MUST allow users to override the time range limit with explicit configuration for extended historical analysis

#### Scenario: Handle missing candle data
- **WHEN** the API returns no candles for the requested period
- **THEN** the node MUST return an empty array with descriptive metadata rather than throwing an error

### Requirement: Query Historical Trades
The integration MUST support querying historical trade data for market analysis with pagination and data volume controls.

#### Scenario: Get trades history for symbol
- **WHEN** a workflow executes the node with the `Get Trades History` operation for a symbol
- **THEN** the node MUST call the `tradesHistory` endpoint and return trade records with price, size, side, and timestamp
- **AND** the operation MUST enforce a default limit of 100 trades per request to prevent excessive data loads
- **AND** the operation MUST support a maximum limit of 1000 trades per request with clear warnings about performance implications

#### Scenario: Pagination support
- **WHEN** requesting trades history with pagination parameters
- **THEN** the node MUST support limit and offset parameters to retrieve paginated results efficiently
- **AND** the node MUST provide metadata indicating total available trades and current page information when available

#### Scenario: Trade direction filtering
- **WHEN** optional side filter is specified (buy/sell)
- **THEN** the node MUST filter returned trades to only the requested direction

#### Scenario: Large history handling
- **WHEN** the API returns the maximum number of trades
- **THEN** the node output MUST indicate if more data is available and suggest pagination approach in metadata
- **AND** the node MUST include continuation guidance (e.g., next offset value) in the response metadata

### Requirement: Query Funding Rate History
The integration MUST provide access to historical funding rate data for perpetual contracts with appropriate data volume controls.

#### Scenario: Get funding rate history
- **WHEN** a workflow executes the node with the `Get Funding History` operation for a symbol
- **THEN** the node MUST call the `fundingHistory` endpoint and return funding rate snapshots with timestamp, rate, and premium
- **AND** the operation MUST enforce a default limit of 100 records per request to prevent excessive data loads
- **AND** the operation MUST support a maximum limit of 1000 records per request with clear warnings about performance implications

#### Scenario: Time range for funding queries
- **WHEN** start and end time parameters are provided
- **THEN** the node MUST filter funding rate records to the specified date range
- **AND** the node MUST validate that the time range does not exceed 90 days by default to prevent excessive data retrieval

#### Scenario: Funding rate aggregation
- **WHEN** retrieving funding history over extended periods
- **THEN** the node MUST return chronologically ordered records without loss of precision in rate values

#### Scenario: Handle symbols without funding
- **WHEN** querying funding history for a symbol that does not pay funding
- **THEN** the node MUST return an empty result set with clear messaging indicating funding is not applicable

### Requirement: Query Public User Information
The integration MUST enable querying public user data without requiring authentication.

#### Scenario: Get user public info
- **WHEN** a workflow executes the node with the `Get User Info` operation and provides a user address
- **THEN** the node MUST call the Info API user endpoint and return public profile information without authentication

#### Scenario: Address validation
- **WHEN** an invalid Ethereum address format is provided for user queries
- **THEN** the node MUST validate the address format and return a descriptive error before making the API call

#### Scenario: User not found handling
- **WHEN** the API indicates the user address has no public data
- **THEN** the node MUST return an empty result structure with appropriate metadata rather than throwing an error

### Requirement: Parameter Validation and Error Handling
The integration MUST validate inputs and handle API errors gracefully across all Info operations.

#### Scenario: Required parameter validation
- **WHEN** a user attempts to execute an operation without required parameters (e.g., symbol, address)
- **THEN** the node MUST validate parameters before API calls and display clear error messages in the n8n editor

#### Scenario: Symbol validation for operations
- **WHEN** an invalid or unsupported symbol is provided
- **THEN** the node MUST validate against available symbols from listings and provide suggestions for valid alternatives
- **AND** the node MUST reuse the same symbol validation logic from Proposal 1's `listSearch/getSymbols.ts` implementation to ensure consistency across both nodes

#### Scenario: Date format validation
- **WHEN** operations accept date/time parameters
- **THEN** the node MUST validate timestamp formats and convert user-friendly date inputs to proper API format

#### Scenario: API error response handling
- **WHEN** the Hyperliquid Info API returns error responses (rate limit, maintenance, invalid request)
- **THEN** the node MUST parse error details and surface descriptive messages guiding users on resolution without crashing workflows

#### Scenario: Network timeout handling
- **WHEN** API requests timeout or network errors occur
- **THEN** the node MUST catch connection failures and return actionable error messages with retry suggestions

### Requirement: Resource Organization and Code Maintainability
The implementation MUST organize Info API operations into logical resource modules for maintainability.

#### Scenario: Resource file structure
- **WHEN** implementing Info Query operations
- **THEN** the code MUST be organized into separate resource files under `resources/info/` directory by operation type (meta, orderBook, candles, trades, funding, user)

#### Scenario: Declarative routing configuration
- **WHEN** defining API calls for Info operations
- **THEN** the node MUST use n8n declarative routing with `INodeProperties` configuration rather than programmatic request handling where possible

#### Scenario: Shared type definitions
- **WHEN** working with API response data
- **THEN** the implementation MUST define TypeScript interfaces for Info API responses in shared type files for consistency and type safety

#### Scenario: Reusable helper functions
- **WHEN** common operations are needed (symbol lookups, date formatting, error parsing)
- **THEN** the implementation MUST reuse shared utility functions from Proposal 1's `shared/utils.ts` where applicable
- **AND** the implementation MUST create additional shared utility functions only when Proposal 1's utilities do not cover the required functionality
- **AND** symbol lookups MUST reuse Proposal 1's `listSearch/getSymbols.ts` implementation to maintain consistency and avoid duplication
