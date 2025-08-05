# Marketplace Contracts

## Overview

The Marketplace Contracts repository contains the legacy on-chain marketplace system for Decentraland NFT trading. While newer off-chain signature-based marketplaces have been developed, these contracts remain important for historical context and backward compatibility.

## Core Contracts

### Marketplace (V1)

**Purpose**: Original marketplace implementation with on-chain order storage.

**Key Specifications**:
- Stores all orders directly on blockchain
- Uses MANA token for all transactions
- Implements order book model
- Charges listing fees for publications
- Supports ERC721 assets only

**Core Functions**:
- `createOrder(asset, price, expiration)`: List asset for sale
- `executeOrder(asset, price)`: Purchase listed asset
- `cancelOrder(asset)`: Remove listing
- `setPublicationFee(fee)`: Configure listing fee
- `setOwnerCut(cut)`: Set marketplace commission

**Order Structure**:
```solidity
struct Order {
    bytes32 id;           // Order identifier
    address seller;       // Asset owner
    address nftAddress;   // NFT contract
    uint256 tokenId;      // Token being sold
    uint256 price;        // Price in MANA
    uint256 expiresAt;    // Order expiration
}
```

### MarketplaceV2

**Purpose**: Enhanced marketplace with additional features and optimizations.

**Key Specifications**:
- Improved gas efficiency over V1
- Adds royalty support for creators
- Better event emission for indexing
- Supports multiple asset types
- Implements bid functionality

**Enhanced Features**:
- `createBid(asset, price, duration)`: Place offer on asset
- `acceptBid(asset, bidder)`: Accept buyer's offer
- `setRoyaltiesManager(manager)`: Configure royalties
- Fingerprint validation for estates
- Improved order management

**Bid System**:
```solidity
struct Bid {
    bytes32 id;           // Bid identifier
    address bidder;       // Buyer address
    address nftAddress;   // Target NFT
    uint256 tokenId;      // Target token
    uint256 price;        // Offered price
    uint256 expiresAt;    // Bid expiration
    bytes fingerprint;    // Estate validation
}
```

## Supporting Systems

### RoyaltiesManager

**Purpose**: Manages creator royalties for secondary sales.

**Key Specifications**:
- Calculates royalty amounts per sale
- Distributes payments to creators
- Configurable royalty percentages
- Supports multiple beneficiaries
- Integrates with marketplace contracts

**Royalty Features**:
- `setRoyalty(token, rate)`: Set royalty percentage
- `getRoyalty(token, amount)`: Calculate royalty amount
- `distributeRoyalty(token, amount)`: Send to creators
- Default rates per collection
- Override rates per token

**Royalty Structure**:
```solidity
struct Royalty {
    address beneficiary;  // Royalty recipient
    uint256 rate;        // Percentage (basis points)
    address token;       // Asset contract
    uint256 tokenId;     // Specific token or 0 for all
}
```

## Common Utilities

### Ownable

**Purpose**: Provides basic ownership and access control.

**Features**:
- Single owner model
- Ownership transfer capability
- Owner-only function modifiers
- Renounce ownership option
- Event emission for transfers

### Pausable

**Purpose**: Emergency stop mechanism for marketplace operations.

**Features**:
- Pause all trading activity
- Owner-controlled pause/unpause
- Affects orders and executions
- Preserves contract state
- Emergency response capability

### NativeMetaTransaction

**Purpose**: Enables gasless transactions through meta-transaction relayers.

**Features**:
- EIP-2771 compliance
- Trusted forwarder support
- Nonce management
- Signature verification
- Context preservation

**Meta-Transaction Flow**:
1. User signs transaction off-chain
2. Relayer submits to contract
3. Contract verifies signature
4. Executes on behalf of user
5. Relayer pays gas fees

## Fee Structure

### Publication Fees

**Purpose**: Charge for creating marketplace listings.

**Implementation**:
- Fixed MANA amount per listing
- Paid when creating order
- Non-refundable
- Sent to DAO treasury
- Configurable by owner

### Owner Cut

**Purpose**: Marketplace commission on successful sales.

**Implementation**:
- Percentage of sale price
- Deducted from seller proceeds
- Sent to marketplace owner
- Typically 2.5% of sale
- Applied after royalties

### Fee Distribution

**Order of Distribution**:
1. Royalties to creators
2. Owner cut to marketplace
3. Remaining to seller
4. All transfers atomic

## Order Management

### Order Creation

**Process**:
1. Seller approves marketplace
2. Pays publication fee
3. Creates order on-chain
4. Order stored in mapping
5. Event emitted for indexing

**Validation**:
- Asset ownership check
- Approval verification
- Fee payment confirmation
- Price validation
- Expiration setting

### Order Execution

**Process**:
1. Buyer approves MANA spending
2. Calls execute with order ID
3. Validates order still active
4. Transfers MANA and NFT
5. Distributes fees and royalties

**Atomic Operations**:
- All transfers in one transaction
- Revert if any transfer fails
- No partial executions
- State consistency maintained

### Order Cancellation

**Process**:
1. Only seller can cancel
2. Order removed from storage
3. Gas refund for storage release
4. Event emission
5. No fee refund

## Security Features

### Reentrancy Protection
- Checks-effects-interactions pattern
- State updates before transfers
- No external calls in critical sections
- Guard modifiers where needed

### Validation Checks
- Asset ownership verification
- Sufficient balance checks
- Approval validations
- Price sanity checks
- Expiration enforcement

### Access Control
- Owner-only administrative functions
- Seller-only order cancellation
- Buyer requirements for execution
- Pause capability for emergencies

## Legacy Considerations

### Migration Path

**From V1 to V2**:
- Cancel V1 orders
- Create new V2 orders
- No automatic migration
- Maintain both contracts
- User-initiated transition

### Backward Compatibility

**Maintained Features**:
- Basic order structure
- MANA payment system
- ERC721 support
- Event signatures
- Core functionality

**Deprecated Features**:
- Direct order storage (moved off-chain)
- High gas costs
- Limited scalability
- No batch operations
- Synchronous only

## Comparison with Off-Chain Marketplace

### Legacy (On-Chain) Approach

**Advantages**:
- Full decentralization
- No signature management
- Simple integration
- Transparent order book
- No external dependencies

**Disadvantages**:
- High gas costs
- Limited scalability
- Storage expenses
- Slower updates
- No private orders

### Modern (Off-Chain) Approach

**Advantages**:
- Minimal gas costs
- Infinite scalability
- Private orders
- Batch operations
- Advanced features

**Disadvantages**:
- Signature complexity
- Off-chain dependency
- Indexer requirements
- More complex integration

## Integration Guidelines

### For Frontend Applications
```javascript
// Order creation
marketplace.createOrder(
    nftAddress,
    tokenId,
    priceInMana,
    expirationTime
)

// Order execution
marketplace.executeOrder(
    nftAddress,
    tokenId,
    priceInMana
)

// Order cancellation
marketplace.cancelOrder(
    nftAddress,
    tokenId
)
```

### For Smart Contracts
- Approve marketplace for NFT transfers
- Ensure MANA approval for purchases
- Handle marketplace events
- Validate order states
- Implement error handling

### For Indexers
- Monitor order events
- Track order state changes
- Index price history
- Calculate volumes
- Generate statistics

## Historical Significance

### Timeline
- **2018**: Initial marketplace launch
- **2019**: V2 with improvements
- **2020**: Royalties addition
- **2021**: Meta-transaction support
- **2022**: Off-chain marketplace transition

### Usage Statistics
- Processed millions in volume
- Thousands of unique traders
- Multiple asset types supported
- Foundation for ecosystem growth
- Proved decentralized commerce model