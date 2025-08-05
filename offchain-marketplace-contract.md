# Offchain Marketplace Contract

## Overview

The Offchain Marketplace Contract is a sophisticated trading system that enables users to create and execute trades using EIP712 signatures, eliminating the need for on-chain order storage and reducing gas costs significantly.

## Core Contracts

### Marketplace (Abstract)

**Purpose**: Base contract providing core marketplace functionality for off-chain signature-based trading.

**Key Specifications**:
- Implements EIP712 signature validation for trade authorization
- Supports multiple asset types (ERC20, ERC721, Collection Items, USD-pegged MANA)
- Manages fee distribution between DAO and royalty recipients
- Provides trade validation and execution logic
- Implements pausable functionality for emergency stops

**Main Functions**:
- `accept`: Executes trades with signature verification
- `acceptWithCoupon`: Executes trades with discount coupons
- `cancel`: Invalidates signatures to prevent trade execution
- Supports batch operations for multiple trades

### DecentralandMarketplaceEthereum

**Purpose**: Ethereum-specific marketplace implementation focusing on LAND, Estates, and Names.

**Key Specifications**:
- Inherits from base Marketplace contract
- Specializes in handling Decentraland's core NFT assets on Ethereum
- Implements network-specific fee structures
- Manages Ethereum-specific asset validation

### DecentralandMarketplacePolygon

**Purpose**: Polygon-specific marketplace implementation optimized for Collection items.

**Key Specifications**:
- Extends base Marketplace with Polygon-specific features
- Integrates with credits system for gasless transactions
- Handles Collection items and wearables trading
- Implements meta-transaction support for improved UX

## Supporting Systems

### CouponManager

**Purpose**: Manages discount coupons for promotional campaigns and special events.

**Key Specifications**:
- Creates and validates discount coupons with signature verification
- Supports collection-specific and general discount types
- Implements usage tracking and expiration logic
- Integrates seamlessly with marketplace trade execution

### CollectionDiscountCoupon

**Purpose**: Specialized coupon implementation for collection-specific discounts.

**Key Specifications**:
- Provides targeted discounts for specific NFT collections
- Validates collection ownership and eligibility
- Supports percentage-based and fixed discount amounts
- Tracks redemption history per user

### CreditsManagerPolygon

**Purpose**: Manages credits system for gasless transactions on Polygon network.

**Key Specifications**:
- Enables users to purchase credits for fee-less trading
- Implements credit deduction during trade execution
- Provides credit balance tracking and management
- Supports credit transfers between users

## Trade Types Supported

### Public Orders
- Open offers available to any buyer
- Listed at fixed prices
- No expiration or time-based expiration

### Bids
- Offers to purchase specific assets
- Buyer initiates with signature
- Seller accepts to complete trade

### Private Orders
- Trades restricted to specific addresses
- Useful for negotiated deals
- Maintains privacy of trade terms

### Auctions
- Time-based bidding mechanism
- Supports reserve prices
- Automatic winner determination

### Batch Trading
- Multiple trades in single transaction
- Gas optimization for bulk operations
- Atomic execution (all or nothing)

## Security Features

### Signature Validation
- EIP712 standard for structured data signing
- Domain separation to prevent cross-contract replay
- Nonce management for signature invalidation

### External Checks
- Pluggable validation system
- Custom logic for asset verification
- Ownership and permission validation

### Fee Management
- Transparent fee calculation
- Automatic distribution to recipients
- Support for multiple fee tiers

## Integration Points

### Asset Contracts
- Direct integration with ERC721 and ERC20 contracts
- Special handling for Decentraland-specific assets
- Collection item verification

### Payment Systems
- MANA token as primary currency
- USD-pegged MANA support
- Credit system integration on Polygon

### Governance
- DAO fee collection
- Upgradeable architecture
- Admin controls for emergency situations