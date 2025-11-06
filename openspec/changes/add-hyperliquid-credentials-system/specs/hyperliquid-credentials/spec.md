## ADDED Requirements

### Requirement: Secure Private Key Storage
The credential system MUST securely store Hyperliquid private keys using n8n's encrypted credential storage and protect keys from exposure in logs, errors, or UI.

#### Scenario: Private key field is password-masked
- **WHEN** a user creates a HyperliquidApi credential in the n8n UI
- **THEN** the private key input field MUST use password masking to hide the key value from display

#### Scenario: Private key is encrypted at rest
- **WHEN** a user saves a HyperliquidApi credential
- **THEN** n8n MUST encrypt the private key before storing it in the database using n8n's built-in encryption system

#### Scenario: Private key never appears in error messages
- **WHEN** a credential-related error occurs during node execution
- **THEN** error messages MUST NOT include the private key value or any portion of it

#### Scenario: Private key never logged
- **WHEN** any operation uses the HyperliquidApi credential
- **THEN** the private key MUST NOT be written to any log files or console output

### Requirement: Private Key Validation
The credential system MUST validate private key format to prevent storage of invalid keys and provide clear error messages for format violations.

#### Scenario: Valid hex format with 0x prefix accepted
- **WHEN** a user enters a private key in format `0x` followed by 64 hexadecimal characters
- **THEN** the credential MUST accept the key as valid

#### Scenario: Valid hex format without 0x prefix accepted
- **WHEN** a user enters a private key as 64 hexadecimal characters without `0x` prefix
- **THEN** the credential MUST accept the key as valid

#### Scenario: Invalid hex characters rejected
- **WHEN** a user enters a private key containing non-hexadecimal characters
- **THEN** the credential MUST reject the key with error message "Private key must be a valid hexadecimal string"

#### Scenario: Invalid length rejected
- **WHEN** a user enters a private key with incorrect length (not 64 characters excluding optional 0x prefix)
- **THEN** the credential MUST reject the key with error message "Private key must be 64 hexadecimal characters (with optional 0x prefix)"

### Requirement: Environment Selection
The credential system MUST support switching between Hyperliquid Mainnet and Testnet environments with correct API endpoint routing.

#### Scenario: Mainnet environment selection
- **WHEN** a user selects "Mainnet" in the environment dropdown
- **THEN** all API calls using this credential MUST route to `https://api.hyperliquid.xyz`

#### Scenario: Testnet environment selection
- **WHEN** a user selects "Testnet" in the environment dropdown
- **THEN** all API calls using this credential MUST route to `https://api.hyperliquid-testnet.xyz`

#### Scenario: Environment dropdown available during creation
- **WHEN** a user creates a new HyperliquidApi credential
- **THEN** the environment dropdown MUST display options "Mainnet" and "Testnet" with "Mainnet" as default

#### Scenario: Environment change updates API endpoint
- **WHEN** a user changes the environment setting in an existing credential
- **THEN** subsequent API calls MUST use the updated environment's API endpoint

### Requirement: API Wallet Support
The credential system MUST support optional API Wallet (agent wallet) mode for delegated trading operations.

#### Scenario: API Wallet address is optional
- **WHEN** a user creates a HyperliquidApi credential
- **THEN** the API Wallet address field MUST be optional and allow empty values

#### Scenario: Valid Ethereum address accepted
- **WHEN** a user enters an API Wallet address in valid Ethereum address format (0x followed by 40 hex characters)
- **THEN** the credential MUST accept the address as valid

#### Scenario: Invalid address format rejected
- **WHEN** a user enters an API Wallet address with invalid Ethereum address format
- **THEN** the credential MUST reject the address with error message "API Wallet address must be a valid Ethereum address (0x followed by 40 hexadecimal characters)"

#### Scenario: Empty API Wallet uses direct wallet mode
- **WHEN** API Wallet address field is empty or not provided
- **THEN** operations MUST use direct wallet mode with the wallet address derived from the private key

#### Scenario: Provided API Wallet uses agent mode
- **WHEN** API Wallet address is provided
- **THEN** operations MUST use agent wallet mode with the specified API Wallet address for transactions

### Requirement: Credential Testing
The credential system MUST provide a test function to verify credential validity through an authenticated API call before use in workflows.

#### Scenario: Test button available after credential creation
- **WHEN** a user completes filling credential fields
- **THEN** a test button MUST be available to validate the credential

#### Scenario: Successful test with valid credentials
- **WHEN** a user clicks test with valid private key and environment
- **THEN** the system MUST make an authenticated API call to Hyperliquid Info API and display success message "Credential test successful"

#### Scenario: Failed test with invalid private key
- **WHEN** a user clicks test with invalid or incorrectly formatted private key
- **THEN** the system MUST display error message "Authentication failed: Invalid private key or unable to derive wallet address"

#### Scenario: Failed test with network error
- **WHEN** a user clicks test but cannot reach Hyperliquid API endpoint
- **THEN** the system MUST display error message "Connection failed: Unable to reach Hyperliquid API. Check network connectivity and environment setting"

#### Scenario: Test respects environment setting
- **WHEN** a user clicks test with environment set to "Testnet"
- **THEN** the test MUST call Testnet API endpoint, not Mainnet

#### Scenario: Test call has no side effects
- **WHEN** credential test is executed
- **THEN** the test MUST only perform read operations without modifying account state or placing orders

### Requirement: Wallet Address Derivation
The credential system MUST derive the Ethereum wallet address from the provided private key for use in API requests requiring user identification.

#### Scenario: Address derived from private key at runtime
- **WHEN** a node executes using HyperliquidApi credential
- **THEN** the system MUST derive the Ethereum wallet address from the private key using viem's `privateKeyToAccount` function

#### Scenario: Derived address cached during execution
- **WHEN** multiple API calls occur in the same node execution
- **THEN** the wallet address MUST be derived once and reused to avoid redundant computation

#### Scenario: Derived address used in API requests
- **WHEN** an API call requires user address parameter
- **THEN** the system MUST use the derived wallet address (or API Wallet address if provided)

### Requirement: Credential Documentation
The credential system MUST provide clear inline documentation and links to help users configure credentials correctly and securely.

#### Scenario: Private key field has descriptive help text
- **WHEN** a user views the private key field
- **THEN** the field MUST display help text: "Your Hyperliquid wallet private key. This is securely encrypted by n8n. Format: 0x followed by 64 hexadecimal characters."

#### Scenario: Environment field has clear labels
- **WHEN** a user views the environment dropdown
- **THEN** the field MUST display help text: "Select Mainnet for live trading or Testnet for testing. Always test on Testnet first."

#### Scenario: API Wallet field explains purpose
- **WHEN** a user views the API Wallet address field
- **THEN** the field MUST display help text: "Optional: Enter API Wallet address for delegated trading. Leave empty for direct wallet operations."

#### Scenario: Documentation link available
- **WHEN** a user creates a HyperliquidApi credential
- **THEN** the credential page MUST include a documentation URL linking to Hyperliquid authentication documentation

#### Scenario: Security best practices documented
- **WHEN** a user views credential configuration
- **THEN** the credential MUST include warnings about private key security and recommendations for using API Wallet for limited-scope operations

### Requirement: Credential Visual Identification
The credential system MUST use appropriate branding to help users identify Hyperliquid credentials in the n8n credential list.

#### Scenario: Hyperliquid icon displayed
- **WHEN** a user views the credential list or credential selector
- **THEN** the HyperliquidApi credential MUST display the Hyperliquid logo icon

#### Scenario: Clear display name
- **WHEN** a user views the credential in any context
- **THEN** the credential MUST show display name "Hyperliquid API" for easy identification

### Requirement: Package Registration
The credential system MUST be properly registered in package.json for discovery by n8n runtime.

#### Scenario: Credential appears in n8n credential list
- **WHEN** the package is installed in an n8n instance
- **THEN** "Hyperliquid API" MUST appear in the list of available credential types

#### Scenario: Credential file is exported correctly
- **WHEN** n8n loads the package
- **THEN** the system MUST successfully load `HyperliquidApi.credentials.ts` without errors

#### Scenario: Credential registered in package.json
- **WHEN** examining package.json
- **THEN** the `n8n.credentials` array MUST include entry for `dist/credentials/HyperliquidApi.credentials.js`
