# Common Contracts

## Overview

The Common Contracts repository provides a collection of reusable abstract contracts that serve as building blocks for the Decentraland ecosystem. These contracts focus on meta-transaction support and signature verification systems, enabling gasless transactions and secure off-chain signature management across various Decentraland projects.

## Meta-Transaction System

### NativeMetaTransaction

**Purpose**: Enables gasless transactions where users sign transaction data off-chain and relayers execute transactions while paying gas fees.

**Key Specifications**:
- Implements EIP-712 standard for structured data signing
- Maintains per-user nonce to prevent replay attacks
- Appends actual user address to call data for proper sender identification
- Compatible with trusted forwarder pattern
- Supports batch meta-transactions through relayers

**Core Functionality**:
- `executeMetaTransaction(userAddress, functionSignature, sigR, sigS, sigV)`: Execute meta-transaction on behalf of user
- `getNonce(user)`: Get current nonce for specific user
- `_getMsgSender()`: Extract actual transaction sender (user or relayer)
- `verify(signer, nonce, chainId, functionSignature, sigR, sigS, sigV)`: Validate signature

**Implementation Details**:
```solidity
struct MetaTransaction {
    uint256 nonce;
    address from;
    bytes functionSignature;
}
```

**Security Features**:
- Domain separator includes chain ID to prevent cross-chain replay
- Nonce increments atomically after each transaction
- Signature validation before execution
- Proper sender context preservation

**Use Cases**:
- Users without ETH can interact with contracts
- Improved UX with sponsored transactions
- Batch operations through relayer services
- Onboarding new users without requiring ETH

### Meta-Transaction Flow

1. **User Signs Transaction**:
   - User creates transaction data off-chain
   - Signs with private key using EIP-712 standard
   - Includes current nonce for replay protection

2. **Relayer Submits**:
   - Relayer receives signed transaction
   - Calls `executeMetaTransaction` with signature
   - Pays gas fees for execution

3. **Contract Executes**:
   - Verifies signature validity
   - Checks and increments nonce
   - Executes function with user as sender
   - Returns execution result

## Signature Verification System

### SignerIndexVerifiable

**Purpose**: Provides signature invalidation at the signer level, allowing users to invalidate all their previous signatures.

**Key Specifications**:
- Each signer has a personal index counter
- Incrementing index invalidates all previous signatures
- User-controlled invalidation mechanism
- Integrates with signature verification flow

**Core Functions**:
- `getSignerIndex(signer)`: Get current index for signer
- `bumpSignerIndex()`: Increment caller's index to invalidate signatures
- `_verifySignerIndex(signer, index)`: Validate index matches current value

**Use Cases**:
- Emergency signature invalidation
- Key rotation scenarios
- Bulk signature cancellation
- Security incident response

**Implementation Pattern**:
```solidity
mapping(address => uint256) private _signerIndex;

function bumpSignerIndex() external {
    _signerIndex[msg.sender]++;
    emit SignerIndexBumped(msg.sender, _signerIndex[msg.sender]);
}
```

### ContractIndexVerifiable

**Purpose**: Provides signature invalidation at the contract level, allowing contract owners to invalidate all signatures globally.

**Key Specifications**:
- Single global index for entire contract
- Only contract owner can bump index
- Invalidates all signatures across all users
- Emergency invalidation mechanism

**Core Functions**:
- `getContractIndex()`: Get current contract-wide index
- `bumpContractIndex()`: Owner increments global index
- `_verifyContractIndex(index)`: Validate index matches current value

**Use Cases**:
- Contract upgrade preparations
- Security vulnerability response
- Emergency pause alternative
- System-wide signature reset

**Security Considerations**:
- Owner-only access control
- Affects all users simultaneously
- Should be used sparingly
- Event emission for transparency

### AssetIndexVerifiable

**Purpose**: Provides granular signature invalidation at the asset level for specific ERC721 tokens.

**Key Specifications**:
- Index per combination of contract, token ID, and signer
- Users can invalidate signatures for specific assets
- Fine-grained control over signature validity
- Preserves other asset signatures

**Core Functions**:
- `getAssetIndex(contract, tokenId, signer)`: Get index for specific asset
- `bumpAssetIndex(contract, tokenId)`: Increment asset index for caller
- `_verifyAssetIndex(contract, tokenId, signer, index)`: Validate asset index

**Data Structure**:
```solidity
mapping(address => // contract address
    mapping(uint256 => // token ID
        mapping(address => // signer
            uint256 // index
        )
    )
) private _assetIndex;
```

**Use Cases**:
- Cancel orders for specific NFTs
- Revoke rental signatures for properties
- Update terms for individual assets
- Asset-specific security responses

## Integration Patterns

### Inheritance Structure

**For Meta-Transactions**:
```solidity
contract MyContract is NativeMetaTransaction {
    function myFunction() external {
        address sender = _getMsgSender(); // Gets actual user
        // Function logic
    }
}
```

**For Signature Verification**:
```solidity
contract MyMarketplace is 
    SignerIndexVerifiable,
    ContractIndexVerifiable,
    AssetIndexVerifiable 
{
    function acceptOrder(Order order, Signature sig) external {
        _verifySignerIndex(order.signer, sig.signerIndex);
        _verifyContractIndex(sig.contractIndex);
        _verifyAssetIndex(order.nft, order.tokenId, order.signer, sig.assetIndex);
        // Process order
    }
}
```

### Combining Features

**Complete Implementation Example**:
```solidity
contract DecentralandContract is 
    NativeMetaTransaction,
    SignerIndexVerifiable,
    ContractIndexVerifiable,
    AssetIndexVerifiable,
    Ownable 
{
    // Inherits all signature and meta-transaction capabilities
    // Can use _getMsgSender() for actual user
    // Users can bump indices to invalidate signatures
    // Owner can bump contract index for emergencies
}
```

## Security Best Practices

### Signature Validation

1. **Always Verify Indices**:
   - Check signer index before processing
   - Validate contract index for global state
   - Verify asset index for specific tokens

2. **Domain Separation**:
   - Include contract address in domain
   - Use chain ID to prevent cross-chain replay
   - Version your signature schemas

3. **Nonce Management**:
   - Never reuse nonces
   - Increment atomically
   - Check nonce before processing

### Meta-Transaction Security

1. **Sender Extraction**:
   - Always use `_getMsgSender()` instead of `msg.sender`
   - Validate sender has required permissions
   - Handle both direct and meta-transaction calls

2. **Signature Verification**:
   - Verify signature before execution
   - Check signature hasn't expired
   - Validate all signature parameters

3. **Gas Considerations**:
   - Set reasonable gas limits
   - Implement circuit breakers
   - Monitor for griefing attacks

## Testing Support

### Mock Contracts

**DummyNativeMetaTransactionImplementor**:
- Test implementation of meta-transaction features
- Demonstrates proper integration patterns
- Includes test functions for validation

**DummyIndexVerifiableImplementor**:
- Test implementation of all index verifiable contracts
- Shows combined inheritance pattern
- Useful for integration testing

**DummyRelayer**:
- Simulates relayer behavior
- Tests meta-transaction flow
- Validates signature handling

## Gas Optimization

### Meta-Transaction Efficiency
- Minimal overhead for meta-transaction processing
- Efficient signature verification using ecrecover
- Optimized storage patterns for nonces

### Index Management Efficiency
- Single storage slot per index
- Minimal gas for bumping indices
- Efficient mapping structures

## Common Use Cases

### Marketplace Orders
- Users sign orders off-chain
- Can invalidate orders by bumping indices
- Contract owner can emergency cancel all orders
- Asset-specific order management

### Rental Agreements
- Sign rental terms without gas
- Invalidate specific property agreements
- Bulk cancellation capabilities
- Emergency invalidation options

### Token Vesting
- Sign vesting agreements off-chain
- Beneficiaries can claim without ETH
- Invalidate vesting signatures if needed
- Contract-wide emergency controls

## Migration and Upgrades

### Adding to Existing Contracts
1. Inherit required abstract contracts
2. Update function signatures to use `_getMsgSender()`
3. Implement index verification in critical functions
4. Deploy with proper initialization

### Version Compatibility
- Contracts are designed for inheritance
- No storage conflicts with proper integration
- Compatible with OpenZeppelin contracts
- Supports various Solidity versions

## Events and Monitoring

### Key Events
- `MetaTransactionExecuted`: Meta-transaction processed
- `SignerIndexBumped`: User invalidated signatures
- `ContractIndexBumped`: Global invalidation
- `AssetIndexBumped`: Asset-specific invalidation

### Monitoring Recommendations
- Track meta-transaction usage
- Monitor index bumping patterns
- Alert on contract index changes
- Analyze signature invalidation trends