## ADDED Requirements
### Requirement: Fetch Hyperliquid Market Data
The integration MUST let n8n workflows retrieve public Hyperliquid market data without manual HTTP configuration.

#### Scenario: Get ticker snapshot
- **WHEN** a workflow executes the `Hyperliquid Market Data` node with the `Get Ticker` operation and selects a supported symbol
- **THEN** the node MUST call the Hyperliquid ticker API and return the latest price information in the node output

#### Scenario: Get order book snapshot
- **WHEN** a workflow executes the node with the `Get Order Book` operation for a symbol
- **THEN** the node MUST return the current bids and asks limited to the depth requested by node parameters

#### Scenario: Get recent trades
- **WHEN** a workflow runs the node with the `Get Recent Trades` operation
- **THEN** the node MUST return a list of recent trades including price, size, and timestamp for the given symbol

#### Scenario: Symbol dropdown
- **WHEN** configuring the node in the n8n editor
- **THEN** the symbol parameter MUST provide a dynamic dropdown sourced from Hyperliquid listings and stay responsive under normal API latency

#### Scenario: Rate limit handling
- **WHEN** the Hyperliquid API responds with a rate limit or maintenance status
- **THEN** the node MUST surface a descriptive error message guiding the user to retry later without crashing the workflow
