# Rentals Contract

## Overview

The Rentals Contract implements a sophisticated on-chain rental system for LAND and Estate NFTs in Decentraland. It enables property owners (lessors) to rent out their virtual real estate to tenants while maintaining ownership, using an efficient signature-based system to minimize gas costs.

## Core Contract

### Rentals

**Purpose**: Main contract managing all rental operations with signature-based agreements.

**Key Specifications**:
- Implements off-chain signature creation with on-chain execution
- Supports both LAND parcels and Estate rentals
- Uses EIP712 standard for structured signature validation
- Manages rental payments in MANA token
- Provides update operator permissions for scene deployment

**Core Components**:
- **Signature Validation**: EIP712-based verification system
- **Payment Processing**: MANA token transfers with fee collection
- **Operator Management**: Controls who can update content
- **Nonce System**: Prevents signature replay attacks
- **Estate Fingerprints**: Ensures estate composition integrity

## Rental Types

### Listings (Lessor-Initiated)

**Purpose**: Property owners create rental offers that tenants can accept.

**Specifications**:
- Lessor signs rental terms off-chain
- Includes price periods and rental duration
- Specifies accepted tenant (or open to anyone)
- Sets update operator permissions
- Defines rental extension options

**Listing Structure**:
```solidity
struct Listing {
    address signer;          // Lessor's address
    address contractAddress; // LAND or Estate contract
    uint256 tokenId;        // Property token ID
    uint256 expiration;     // Listing expiration
    uint256[3] nonces;      // Signature invalidation
    uint256[] pricePerDay;  // Daily rental prices
    uint256[] maxDays;      // Maximum rental periods
    uint256[] minDays;      // Minimum rental periods
    address target;         // Specific tenant or address(0)
}
```

### Offers (Tenant-Initiated)

**Purpose**: Tenants create rental requests that property owners can accept.

**Specifications**:
- Tenant signs desired rental terms
- Specifies target property and duration
- Includes offered payment amount
- Requests update operator status
- Can target specific properties

**Offer Structure**:
```solidity
struct Offer {
    address signer;          // Tenant's address
    address contractAddress; // LAND or Estate contract
    uint256 tokenId;        // Property token ID
    uint256 expiration;     // Offer expiration
    uint256[3] nonces;      // Signature invalidation
    uint256 pricePerDay;    // Offered daily price
    uint256 rentalDays;     // Desired rental period
    address operator;       // Requested operator address
}
```

## Pricing System

### Price Periods

**Purpose**: Flexible pricing based on rental duration.

**Implementation**:
- Multiple price tiers for different rental lengths
- Longer rentals can have different daily rates
- Minimum and maximum day constraints per tier
- Automatic tier selection based on rental days

**Example Configuration**:
- 1-7 days: 100 MANA per day
- 8-30 days: 80 MANA per day
- 31-90 days: 60 MANA per day
- 91+ days: 50 MANA per day

### Fee Structure

**DAO Fee Collection**:
- Configurable percentage of rental payment
- Automatically transferred to DAO treasury
- Separate fee rates for different asset types
- Fee exemptions for specific addresses

**Payment Distribution**:
- Lessor receives rental payment minus DAO fee
- DAO receives configured percentage
- All transfers happen atomically
- Failed transfers revert entire transaction

## Signature Management

### Nonce System

**Three-Level Nonce Hierarchy**:

1. **Contract Nonce**: Invalidates all signatures from an address
2. **Signer Nonce**: Invalidates all signatures for specific signer
3. **Asset Nonce**: Invalidates signatures for specific asset

**Nonce Operations**:
- `bumpContractNonce()`: Invalidate all your signatures
- `bumpSignerNonce(signer)`: Invalidate signatures from signer
- `bumpAssetNonce(contract, tokenId)`: Invalidate asset signatures

### Signature Verification

**EIP712 Implementation**:
- Domain separator prevents cross-contract replay
- Structured data hashing for security
- Chain ID verification for network safety
- Recovers signer from signature
- Validates signer authorization

## Update Operator System

### Operator Permissions

**Purpose**: Grant content deployment rights to tenants.

**Functionality**:
- Tenants receive update operator status
- Can deploy scenes and content to rented property
- Cannot transfer or sell the property
- Permissions automatically revoked after rental
- Original operator restored after rental ends

### Content Management

**During Rental**:
- Tenant can update parcel/estate content
- Deploy scenes through builder
- Modify estate metadata (if permitted)
- Original owner cannot update content
- Automatic permission transfer

**After Rental**:
- Operator permissions return to owner
- Tenant loses update capabilities
- Content remains unless owner changes
- Smooth transition of control

## Estate-Specific Features

### Fingerprint System

**Purpose**: Ensure estate composition doesn't change during rental.

**Implementation**:
- Fingerprint captures estate's LAND parcels
- Verified at rental acceptance
- Prevents adding/removing LANDs during rental
- Protects tenant from estate modifications
- Automatically validated on-chain

**Fingerprint Calculation**:
- Hashes all LAND coordinates in estate
- Includes parcel count
- Deterministic and verifiable
- Changes invalidate rental signatures

### Estate Validation

**Additional Checks**:
- Validates all parcels still in estate
- Confirms estate hasn't been split
- Verifies owner hasn't changed
- Ensures estate contract is valid

## Rental Extensions

### Extension Mechanism

**Purpose**: Allow rental period extensions before expiration.

**Features**:
- Extend using same price tier
- Must extend before rental ends
- Payment processed immediately
- Operator permissions maintained
- No gap in rental coverage

**Extension Rules**:
- Can only extend active rentals
- Must use original rental terms
- Cannot change operator during extension
- Same fee structure applies

## Integration Interfaces

### IERC721Rentable

**Purpose**: Interface for rentable NFT contracts.

**Required Functions**:
- `setUpdateOperator`: Grant content update permissions
- `getUpdateOperator`: Query current operator
- Estate-specific functions for fingerprints
- Standard ERC721 functions

## Security Features

### Signature Security
- EIP712 standard compliance
- Domain separation
- Replay attack prevention
- Expiration timestamps
- Multi-level nonce system

### Payment Security
- Atomic payment processing
- Revert on transfer failure
- Fee calculation validation
- Balance checks before transfer
- No re-entrancy vulnerabilities

### Access Control
- Only property owner can accept offers
- Only tenant with MANA can accept listings
- Signature verification for all actions
- Time-based expiration checks
- Estate fingerprint validation

## Gas Optimization

### Off-Chain Signatures
- Create rental terms off-chain
- Single transaction to execute
- No on-chain storage of offers
- Reduced gas for rental creation
- Batch processing capabilities

### Efficient Storage
- Packed struct storage
- Minimal storage updates
- Reuse of existing operators
- Optimized loop operations
- Event-based indexing

## Use Cases

### Short-Term Rentals
- Event hosting in virtual world
- Temporary brand activations
- Pop-up experiences
- Gallery exhibitions
- Conference venues

### Long-Term Rentals
- Business locations
- Permanent brand presence
- Community spaces
- Development projects
- Investment properties

### Development Rights
- Rent to builders for scene creation
- Temporary development permissions
- Content creation services
- Experience design contracts
- Maintenance agreements

## For Property Owners

### Creating Listings
1. Decide rental terms (price, duration)
2. Sign listing message off-chain
3. Share signature with potential tenants
4. Receive MANA when accepted
5. Property returns after rental period

### Accepting Offers
1. Review tenant offers
2. Verify terms are acceptable
3. Call accept function with signature
4. Receive immediate payment
5. Monitor rental period

## For Tenants

### Accepting Listings
1. Find available properties
2. Review rental terms
3. Ensure sufficient MANA balance
4. Accept listing with signature
5. Receive operator permissions

### Creating Offers
1. Identify desired property
2. Determine rental terms
3. Sign offer message
4. Share with property owner
5. Wait for acceptance