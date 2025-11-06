## Why
Hyperliquid API requires cryptographic signature authentication using private keys for all account-related and trading operations. Today the package has no secure way to store and manage private keys, blocking implementation of authenticated endpoints like account queries, trading, and withdrawals. Adding a credentials system enables secure private key storage through n8n's encrypted credential system and establishes the authentication foundation for all future trading features.

## What Changes
- Create `HyperliquidApi` credential class implementing `ICredentialType`
- Add secure private key storage with n8n's built-in encryption
- Support environment switching between Mainnet and Testnet networks
- Support API Wallet (agent wallet) mode for delegated trading
- Implement private key format validation (EVM-compatible hex format)
- Add credential test functionality to verify key validity
- Integrate wallet management library (viem or ethers) for signature operations
- Document security best practices and setup instructions

## Impact
- Affected specs: `hyperliquid-credentials` (new capability)
- Affected code:
  - New file: `credentials/HyperliquidApi.credentials.ts`
  - Future nodes will reference this credential for authenticated operations
- Dependencies: Required by all Phase 3+ proposals (account queries, trading, withdrawals)
- Security considerations: Handles sensitive cryptographic material requiring strict security review
- Adds external dependencies: `viem` or `ethers` for wallet operations
