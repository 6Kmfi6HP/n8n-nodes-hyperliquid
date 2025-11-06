## ADDED Requirements
### Requirement: Fetch Hyperliquid Market Data
The integration MUST let n8n workflows retrieve public Hyperliquid market data without manual HTTP configuration. The node MUST support both perpetual futures and spot market data queries.

#### Scenario: Get all mid prices
- **WHEN** a workflow executes the `Hyperliquid Market Data` node with the `Get All Mids` operation
- **THEN** the node MUST call the Hyperliquid Info API with `{"type": "allMids"}` and return a dictionary of all symbol mid-prices in the node output
- **AND** the response MUST include both perpetual and spot market mids when using the default perp dex

#### Scenario: Get order book snapshot
- **WHEN** a workflow executes the node with the `Get Order Book` operation for a symbol
- **THEN** the node MUST call the API with `{"type": "l2Book", "coin": "<symbol>"}` and return the current bids and asks with price, size, and order count for each level
- **AND** the response MUST include separate arrays for bids and asks sorted by price priority

#### Scenario: Get recent trades
- **WHEN** a workflow runs the node with the `Get Recent Trades` operation and specifies a symbol
- **THEN** the node MUST return a list of recent trades including price, size, timestamp, side (buy/sell), and trade hash
- **AND** the number of trades returned MUST respect the optional limit parameter (default: 20, max: 100)

#### Scenario: Get candle snapshot
- **WHEN** a workflow executes the node with the `Get Candle Snapshot` operation for a symbol and interval
- **THEN** the node MUST call the API with candle parameters and return OHLCV (Open, High, Low, Close, Volume) data
- **AND** the response MUST support time range filtering via optional startTime and endTime parameters
- **AND** the interval parameter MUST support standard values: 1m, 3m, 5m, 15m, 30m, 1h, 2h, 4h, 8h, 12h, 1d, 3d, 1w, 1M

#### Scenario: Get market metadata
- **WHEN** a workflow executes the node with the `Get Market Metadata` operation
- **THEN** the node MUST return universe information, asset contexts, and metadata including tick sizes, lot sizes, and asset IDs
- **AND** the response MUST include information for both perpetuals and spot markets

#### Scenario: Get spot metadata
- **WHEN** a workflow executes the node with the `Get Spot Metadata` operation
- **THEN** the node MUST return spot market token details including token names, decimals, token IDs, and trading pair information

#### Scenario: Symbol dropdown for perpetuals
- **WHEN** configuring the node in the n8n editor and a symbol parameter is required
- **THEN** the symbol parameter MUST provide a dynamic dropdown sourced from Hyperliquid perpetual listings
- **AND** the dropdown MUST stay responsive under normal API latency (< 2 seconds)
- **AND** the dropdown MUST cache results to avoid excessive API calls

#### Scenario: Symbol dropdown for spot markets
- **WHEN** configuring the node and spot market symbols are available
- **THEN** the symbol dropdown MUST include both perpetual and spot market symbols
- **AND** spot symbols MUST be clearly distinguished (e.g., "PURR/USDC" format)

#### Scenario: Environment selection
- **WHEN** configuring the node
- **THEN** the node MUST provide an environment parameter (Mainnet/Testnet) with default to Mainnet
- **AND** the API base URL MUST switch accordingly (Mainnet: `https://api.hyperliquid.xyz`, Testnet: `https://api.hyperliquid-testnet.xyz`)

#### Scenario: Rate limit handling
- **WHEN** the Hyperliquid API responds with a rate limit or maintenance status
- **THEN** the node MUST surface a descriptive error message guiding the user to retry later without crashing the workflow
- **AND** the error message MUST include suggested retry delay information when available

#### Scenario: Invalid symbol handling
- **WHEN** a workflow provides an invalid or unsupported symbol
- **THEN** the node MUST return a clear error message indicating the symbol is not found
- **AND** the error MUST suggest checking available symbols via the metadata operation

#### Scenario: Network error handling
- **WHEN** a network error occurs during API request
- **THEN** the node MUST return a user-friendly error message
- **AND** the error MUST suggest checking network connectivity and API status

#### Scenario: Response data format
- **WHEN** the node successfully retrieves market data
- **THEN** all numeric values (prices, sizes, volumes) MUST be returned as strings to preserve precision
- **AND** timestamps MUST be returned in milliseconds (epoch format)
- **AND** the response structure MUST be consistent and suitable for n8n workflow consumption
