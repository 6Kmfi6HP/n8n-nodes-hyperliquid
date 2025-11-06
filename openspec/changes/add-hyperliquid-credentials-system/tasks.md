## 1. Dependency Setup
- [ ] 1.1 Research and decide between viem vs ethers for wallet management
- [ ] 1.2 Add chosen wallet library to `package.json` dependencies
- [ ] 1.3 Run `npm install` to update lock file

## 2. Credential Class Implementation
- [ ] 2.1 Create `credentials/HyperliquidApi.credentials.ts` file
- [ ] 2.2 Implement `ICredentialType` interface with required properties
- [ ] 2.3 Add private key input field with password masking
- [ ] 2.4 Add environment dropdown (Mainnet/Testnet)
- [ ] 2.5 Add optional API Wallet address field
- [ ] 2.6 Add Hyperliquid icon reference

## 3. Private Key Validation
- [ ] 3.1 Implement private key format validation (hex format with 0x prefix)
- [ ] 3.2 Add validation error messages for invalid formats
- [ ] 3.3 Test validation with valid and invalid key formats

## 4. Credential Testing
- [ ] 4.1 Implement test request to verify credential validity
- [ ] 4.2 Configure test to call a simple authenticated endpoint
- [ ] 4.3 Handle test success and failure responses
- [ ] 4.4 Add clear error messages for failed authentication

## 5. Documentation
- [ ] 5.1 Add inline documentation strings for all credential fields
- [ ] 5.2 Add documentation URL pointing to Hyperliquid authentication docs
- [ ] 5.3 Document security best practices in code comments

## 6. Package Registration
- [ ] 6.1 Register credential in `package.json` under `n8n.credentials` array
- [ ] 6.2 Verify credential appears in n8n credential list

## 7. Testing and Validation
- [ ] 7.1 Run `npm run lint` and fix any issues
- [ ] 7.2 Run `npm run build` to verify compilation
- [ ] 7.3 Test credential creation in local n8n instance
- [ ] 7.4 Test credential with both Mainnet and Testnet
- [ ] 7.5 Validate with `openspec validate add-hyperliquid-credentials-system --strict`
