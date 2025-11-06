## Context
Hyperliquid is an EVM-compatible decentralized derivatives exchange that requires cryptographic signatures for all authenticated operations. The platform uses Ethereum-style ECDSA signatures with secp256k1 keys. Users must sign requests with their private keys to prove ownership of trading accounts.

n8n provides a built-in credential system with encryption at rest and secure storage. Credentials implement the `ICredentialType` interface and can include validation, testing, and authentication logic. The n8n runtime automatically handles decryption and provides credentials to nodes at execution time.

### Key Constraints
- Private keys are highly sensitive cryptographic material
- Credentials must work in n8n's serverless execution model
- n8n credential validation requires synchronous operations
- Hyperliquid supports both direct wallet operations and API Wallet (agent) delegation
- Must support switching between Mainnet (production) and Testnet environments

### Stakeholders
- Workflow developers using Hyperliquid trading features
- Security team (sensitive key material handling)
- Future node implementations requiring authentication

## Goals / Non-Goals

### Goals
- Secure storage of Hyperliquid private keys using n8n's encryption
- Support both Mainnet and Testnet environments
- Support API Wallet (agent wallet) mode for delegated operations
- Validate private key format before storage
- Test credential validity through authenticated API call
- Minimal external dependencies for wallet operations
- Clear documentation and error messages

### Non-Goals
- Key generation (users must provide existing keys)
- Hardware wallet integration (n8n limitation)
- Multi-signature wallets
- Key rotation mechanisms (handled externally)
- Custom encryption beyond n8n's built-in system
- Mnemonic phrase support (direct private key only)

## Decisions

### Decision 1: Wallet Library Selection (viem vs ethers)

**Chosen**: viem

**Rationale**:
- **Modern TypeScript**: viem is TypeScript-first with superior type safety
- **Tree-shakeable**: Smaller bundle size through modular design
- **Performance**: Faster than ethers.js in benchmarks
- **Active development**: Strong ecosystem momentum and maintenance
- **EVM compatibility**: Full support for Ethereum-style signatures required by Hyperliquid

**Alternatives considered**:
1. **ethers.js**: More mature ecosystem but larger bundle size and TypeScript limitations
2. **web3.js**: Older API design, less suitable for modern TypeScript projects
3. **Native crypto**: Would require manual implementation of secp256k1 signatures (high risk)

**Trade-offs**:
- viem is newer with smaller community compared to ethers
- Some developers may be more familiar with ethers API
- Acceptable trade-off for better performance and type safety

### Decision 2: Private Key Storage Format

**Chosen**: Hex string with 0x prefix (standard Ethereum format)

**Rationale**:
- Industry standard for EVM-compatible private keys
- Compatible with wallet export formats (MetaMask, etc.)
- Simple validation with regex pattern
- Direct compatibility with viem's `privateKeyToAccount` function

**Alternatives considered**:
1. **Base64 encoding**: Non-standard, would confuse users
2. **Raw bytes**: Difficult to input and validate in web UI
3. **Mnemonic phrases**: Would require additional derivation path configuration

### Decision 3: Credential Structure

**Chosen**: Single credential type with environment selector

**Properties**:
```typescript
{
  privateKey: string;      // Required, password-masked
  environment: string;     // Dropdown: 'mainnet' | 'testnet'
  apiWalletAddress?: string; // Optional, for agent wallet mode
}
```

**Rationale**:
- Simple mental model (one credential per trading account)
- Environment switching without credential duplication
- API Wallet support for advanced use cases
- Follows n8n credential patterns (see GitHub OAuth2 example)

**Alternatives considered**:
1. **Separate credential types per environment**: Duplicates code and confuses users
2. **Multiple keys in one credential**: Complex UX, unclear which key to use
3. **Environment as node parameter**: Would require credential duplication per environment

### Decision 4: Credential Testing Strategy

**Chosen**: Test with authenticated Info API call (userState query)

**Rationale**:
- Info API has lower rate limits risk compared to Exchange API
- userState endpoint requires authentication but has no side effects
- Fast response time for credential validation
- Works for both Mainnet and Testnet

**Alternatives considered**:
1. **Local signature verification**: Doesn't verify API connectivity
2. **Exchange API test**: Risk of rate limiting or side effects
3. **Public API test**: Doesn't verify authentication works

**Implementation**:
```typescript
test: ICredentialTestRequest = {
  request: {
    baseURL: '={{$credentials.environment === "testnet" ? "https://api.hyperliquid-testnet.xyz" : "https://api.hyperliquid.xyz"}}',
    url: '/info',
    method: 'POST',
    body: {
      type: 'clearinghouseState',
      user: '={{$credentials.walletAddress}}'  // Derived from private key
    },
    headers: {
      'Content-Type': 'application/json'
    }
  }
};
```

### Decision 5: Validation Rules

**Chosen**: Synchronous format validation only

**Rules**:
1. Private key must be 64-66 characters (0x prefix optional)
2. Must be valid hexadecimal string
3. API Wallet address (if provided) must be valid Ethereum address format

**Rationale**:
- n8n credential validation is synchronous
- Format validation catches most user errors
- Full validation happens during credential test
- Balances security with UX constraints

**Regex pattern**: `/^(0x)?[0-9a-fA-F]{64}$/`

## Architecture

### Component Diagram
```
┌─────────────────────────────────────────┐
│ n8n Credential System                   │
│ (Encrypted Storage)                     │
└─────────────┬───────────────────────────┘
              │
              │ Provides encrypted credentials
              ▼
┌─────────────────────────────────────────┐
│ HyperliquidApi Credentials              │
│                                          │
│ - privateKey (string, password)         │
│ - environment (mainnet/testnet)         │
│ - apiWalletAddress (optional)           │
│                                          │
│ + validate(): boolean                   │
│ + test(): Promise<boolean>              │
└─────────────┬───────────────────────────┘
              │
              │ Uses for wallet operations
              ▼
┌─────────────────────────────────────────┐
│ viem Library                             │
│                                          │
│ - privateKeyToAccount()                 │
│ - signMessage()                          │
│ - signTransaction()                     │
└─────────────┬───────────────────────────┘
              │
              │ Generates signatures for
              ▼
┌─────────────────────────────────────────┐
│ Hyperliquid API                          │
│                                          │
│ - Info API (queries)                    │
│ - Exchange API (trading)                │
└─────────────────────────────────────────┘
```

### Credential Flow
1. **Creation**: User enters private key in n8n UI
2. **Validation**: Format validated synchronously
3. **Storage**: n8n encrypts and stores credential
4. **Testing**: Optional test call to verify authentication
5. **Usage**: Nodes receive decrypted credential at runtime
6. **Signing**: viem generates signature for each API request

### Security Layers
1. **n8n encryption**: Credentials encrypted at rest in n8n database
2. **Password masking**: Private key hidden in UI with typeOptions.password
3. **Format validation**: Prevents storage of malformed keys
4. **Test validation**: Verifies key validity before use
5. **No logging**: Private keys never logged or exposed in errors

## Risks / Trade-offs

### Risk 1: Private Key Exposure
**Risk**: Private keys are sensitive; exposure could lead to fund loss

**Mitigation**:
- Rely on n8n's built-in encryption (battle-tested)
- Use password field type to mask keys in UI
- Never log private keys in error messages
- Document security best practices in credential description
- Recommend using API Wallet for limited-scope operations

**Trade-off**: Cannot use hardware wallets due to n8n's server-side execution model

### Risk 2: Key Format Confusion
**Risk**: Users may enter invalid key formats (with/without 0x, wrong length)

**Mitigation**:
- Clear field description with format example
- Regex validation with helpful error messages
- Support both 0x-prefixed and non-prefixed formats
- Test function provides immediate feedback

### Risk 3: Environment Mismatch
**Risk**: User might use Testnet key on Mainnet or vice versa

**Mitigation**:
- Clear environment labels and descriptions
- Test function validates against selected environment
- Separate base URLs for each environment
- Error messages indicate environment in use

### Risk 4: API Wallet Complexity
**Risk**: API Wallet (agent) mode adds configuration complexity

**Mitigation**:
- Make API Wallet address optional (defaults to direct wallet)
- Provide clear documentation on when to use API Wallet
- Include setup instructions in field description
- Test function works for both modes

### Risk 5: Dependency Size
**Risk**: viem adds ~500KB to bundle size

**Mitigation**:
- viem is tree-shakeable; only import needed functions
- Acceptable size increase for critical functionality
- All authenticated Hyperliquid features depend on this
- Alternative (ethers) has similar size footprint

**Trade-off**: Cannot avoid wallet library dependency for EVM signatures

## Migration Plan

### Phase 1: Initial Implementation (This Proposal)
1. Add viem dependency to package.json
2. Implement HyperliquidApi credential class
3. Register credential in package.json
4. Test in local n8n environment
5. Document setup instructions

### Phase 2: Integration with Nodes (Future Proposals)
1. Update SDK integration (Proposal 4) to use credentials
2. Add credential parameter to account query nodes (Proposal 5)
3. Add credential parameter to trading nodes (Proposals 6-10)
4. Each node validates credential presence and format

### Phase 3: Enhanced Features (Optional Future)
1. Add vault address support (if Hyperliquid adds this feature)
2. Add session token caching (if API supports)
3. Add key rotation workflow documentation
4. Add multi-credential support for trading multiple accounts

### Rollback Plan
If credential system has critical issues:
1. Remove HyperliquidApi.credentials.ts
2. Remove credential registration from package.json
3. Revert viem dependency addition
4. Delay authenticated feature development until resolved
5. No impact on existing public API nodes (Proposals 1-2)

## Open Questions

### Q1: Should we support mnemonics in addition to private keys?
**Decision needed by**: Implementation start
**Implications**: Would require derivation path configuration, adds complexity
**Recommendation**: Start with private keys only, add mnemonics in future proposal if demand exists

### Q2: Should we add rate limiting configuration to credentials?
**Decision needed by**: Before SDK integration (Proposal 4)
**Implications**: Credentials would store rate limit preferences
**Recommendation**: Handle rate limiting in SDK layer, not credentials

### Q3: Should we implement credential rotation workflows?
**Decision needed by**: After initial deployment
**Implications**: Would require workflow documentation and best practices
**Recommendation**: Document manual rotation process, automated rotation is out of scope

### Q4: Should we validate key ownership at credential creation?
**Decision needed by**: Implementation start
**Implications**: Would require API call during credential setup
**Recommendation**: Use test function for validation (user-triggered), not automatic

## References

### External Documentation
- [Hyperliquid API Authentication](https://hyperliquid.gitbook.io/hyperliquid-docs/for-developers/api/authentication)
- [n8n Credential Development](https://docs.n8n.io/integrations/creating-nodes/code/create-credentials/)
- [viem Account Signing](https://viem.sh/docs/accounts/local)
- [EIP-191 Signed Data Standard](https://eips.ethereum.org/EIPS/eip-191)

### Internal References
- `credentials/GithubIssuesApi.credentials.ts` - Example credential implementation
- `openspec/project.md` - Project conventions
- `openspec/HYPERLIQUID_IMPLEMENTATION_ROADMAP.md` - Overall implementation plan
