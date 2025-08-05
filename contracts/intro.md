# Decentraland Smart Contracts Documentation

## Overview

Welcome to the comprehensive documentation of Decentraland's smart contract ecosystem. This documentation covers the entire blockchain infrastructure that powers Decentraland's virtual world, from basic token standards to sophisticated marketplace systems and administrative tools.

Decentraland's contracts are organized into several logical groups, each serving specific purposes within the ecosystem. This guide will help you understand how these contracts work together to create a decentralized virtual world with true digital ownership.

## Contract Architecture

### Foundation Layer
The foundation of Decentraland starts with basic building blocks that other contracts build upon:

- **[ERC721 Contracts](erc721.md)**: Standard NFT implementation providing the base for all non-fungible tokens in Decentraland
- **[MANA Token](mana.md)**: The native ERC20 cryptocurrency that powers the entire economy
- **[Common Contracts](common-contracts.md)**: Reusable components for meta-transactions and signature verification

### Virtual World Infrastructure
The core contracts that define and manage the virtual world:

- **[LAND Contracts](land.md)**: Registry and management system for virtual land parcels and estates
- **[Pass-Through Contracts](pass-through.md)**: Proxy system for controlled access to Estate Registry, primarily for Districts

### Digital Assets & Collectibles
Systems for creating and managing wearables and other digital items:

- **[Wearables Contracts](wearables-contracts.md)**: Comprehensive system for avatar customization items, including collections, rarities, and cross-chain bridges

### Economic Systems
The marketplace and trading infrastructure:

- **[Marketplace Contracts](marketplace-contracts.md)**: Legacy on-chain marketplace with order book model
- **[Offchain Marketplace](offchain-marketplace-contract.md)**: Modern signature-based marketplace for gas-efficient trading
- **[Rentals Contract](rentals-contract.md)**: On-chain rental system for LAND and Estates

### Supporting Infrastructure
Additional systems that enhance the ecosystem:

- **[Vestings Builder](vestings-builder.md)**: Token vesting system for team, investors, and community allocations
- **[Auxiliary Contracts](aux-contracts.md)**: Administrative tools for system maintenance and emergency operations

## Contract Groups Explained

### 1. Foundation Contracts

These are the fundamental building blocks that other contracts inherit from or interact with:

#### ERC721 Implementation
- Provides NFT functionality for all Decentraland assets
- Implements enumerable and metadata extensions
- Foundation for LAND, Estates, Names, and Wearables

#### MANA Token
- ERC20 token serving as the primary currency
- Deflationary mechanics through burning
- Used across all economic interactions

#### Common Contracts
- Meta-transaction support for gasless operations
- Signature verification systems
- Reusable components for other contracts

### 2. Virtual World Contracts

These contracts define the virtual world itself:

#### LAND Registry
- Each parcel is a unique NFT with coordinates
- Stores metadata for content deployment
- Update operator system for delegated building

#### Estate Registry
- Groups multiple LAND parcels under single ownership
- Enables bulk operations and management
- Maintains relationships between estates and parcels

#### Pass-Through System
- Provides controlled access to Estate operations
- Used by Districts for community governance
- Method filtering for security

### 3. Digital Asset Systems

Managing wearables and collectibles:

#### Collections Framework
- Item-based minting system
- Rarity tiers with supply limits
- Creator and third-party support

#### Bridge Infrastructure
- Cross-chain transfers between Ethereum and Polygon
- Maintains asset integrity across chains
- Synchronizes metadata and ownership

### 4. Trading & Economic Systems

The marketplace evolution from on-chain to off-chain:

#### Legacy Marketplace
- Traditional on-chain order storage
- Direct peer-to-peer trading
- Historical foundation of Decentraland commerce

#### Modern Marketplace
- Signature-based trading for gas efficiency
- Support for various trade types (orders, bids, auctions)
- Coupon and credit systems for enhanced UX

#### Rental System
- Temporary LAND/Estate usage rights
- Signature-based agreements
- Update operator delegation for builders

### 5. Administrative & Support Systems

Tools for ecosystem management:

#### Vesting Contracts
- Complex vesting schedules for token distribution
- Revocable and pausable options
- Batch deployment capabilities

#### Auxiliary Tools
- Emergency administrative functions
- Token burning mechanisms
- Contract cleanup utilities

## Integration Patterns

### For Developers

When building on Decentraland, you'll typically interact with contracts in this order:

1. **Start with MANA**: Understand the payment token
2. **Learn NFT basics**: Study ERC721 implementation
3. **Explore LAND**: Understand property ownership
4. **Integrate Marketplaces**: Enable trading in your dApp
5. **Add Meta-transactions**: Improve UX with gasless transactions

### For Smart Contract Developers

When developing new contracts:

1. **Inherit Common Contracts**: Use meta-transaction and signature systems
2. **Follow ERC Standards**: Maintain compatibility
3. **Integrate MANA**: Use as payment method
4. **Support Marketplaces**: Enable trading functionality
5. **Plan for Upgrades**: Use proxy patterns where appropriate

## Security Considerations

All Decentraland contracts follow security best practices:

- **Access Control**: Role-based permissions
- **Pausability**: Emergency stop mechanisms
- **Upgrade Paths**: Proxy patterns for critical contracts
- **Signature Security**: EIP-712 standard implementation
- **Gas Optimization**: Efficient storage patterns

## Getting Started

### For Users
Start with understanding [MANA Token](mana.md) and [LAND Contracts](land.md) to grasp the basics of ownership and transactions.

### For Developers
Begin with [Common Contracts](common-contracts.md) to understand the infrastructure patterns, then explore specific systems based on your needs.

### For Traders
Focus on [Offchain Marketplace](offchain-marketplace-contract.md) for modern trading and [Rentals Contract](rentals-contract.md) for property leasing.

### For Creators
Study [Wearables Contracts](wearables-contracts.md) to understand how to create and manage digital collectibles.

## Contract Addresses

Each contract documentation includes deployment addresses for:
- Ethereum Mainnet
- Polygon/Matic Network
- Respective Testnets

Always verify contract addresses from official Decentraland sources before interacting with them.

## Version History

Decentraland's contracts have evolved through several iterations:

- **2017-2018**: Initial MANA token and LAND contracts
- **2019-2020**: Marketplace and Estate systems
- **2021-2022**: Polygon integration and wearables
- **2023-2024**: Off-chain marketplaces and optimizations

## Support and Resources

- **GitHub**: Source code for all contracts
- **Discord**: Developer community support
- **Forum**: Technical discussions
- **Documentation**: This comprehensive guide

## Contributing

The Decentraland smart contract ecosystem is open source. Contributions, bug reports, and improvements are welcome through the respective GitHub repositories.