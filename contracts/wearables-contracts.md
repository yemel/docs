# Wearables Contracts

## Overview

The Wearables Contracts repository contains the comprehensive system for creating, managing, and trading wearable items and collectibles in Decentraland. This includes avatar customization items, emotes, and other digital collectibles that can be worn or used by avatars in the virtual world.

## Collection Contracts (V2)

### ERC721CollectionV2

**Purpose**: Main collection contract for wearables and collectibles with item-based minting system.

**Key Specifications**:
- Implements item-based minting where items are templates for NFTs
- Each item has configurable rarity and maximum supply
- Supports lazy minting to reduce gas costs
- Integrates beneficiary system for revenue distribution
- Implements rescue mechanism for stuck tokens

**Main Functions**:
- `addItems`: Add new item templates to collection
- `mintItems`: Mint NFTs based on item templates
- `rescueItems`: Recover items sent to contract by mistake
- `setApproved`: Control minting permissions
- `setItemsBeneficiary`: Configure revenue recipients

### ERC721BaseCollectionV2

**Purpose**: Base implementation providing core collection functionality.

**Key Specifications**:
- Extends standard ERC721 with collection-specific features
- Implements role-based access control (creator, manager, minter)
- Provides metadata management per item
- Supports batch operations for efficiency
- Integrates committee approval system

**Access Roles**:
- **Creator**: Can add items and modify collection
- **Manager**: Can manage items and approvals
- **Minter**: Can mint items within limits
- **Item Manager**: Can edit specific item properties

## Factory System

### ERC721CollectionFactoryV2

**Purpose**: Factory contract for deploying new collection contracts with standardized configuration.

**Key Specifications**:
- Deploys minimal proxy contracts to reduce deployment costs
- Implements beacon pattern for upgradeable logic
- Manages collection creation fees and limits
- Tracks deployed collections in registry
- Supports multiple collection versions

**Factory Features**:
- `createCollection`: Deploy new collection with initial configuration
- Validates collection parameters
- Enforces creation limits and fees
- Registers collections for discovery
- Supports third-party collections

## Third-Party Integration

### ThirdPartyRegistry

**Purpose**: Registry system for managing third-party creators and their collections.

**Key Specifications**:
- Manages approved third-party creators
- Implements slot-based item limits
- Tracks consumption across collections
- Provides metadata for third-party identification
- Integrates with committee governance

**Third-Party Features**:
- Application and approval process
- Slot purchasing mechanism
- Usage tracking and limits
- Metadata management (name, description, URLs)
- Rule enforcement for compliance

### Committee

**Purpose**: Governance contract managing third-party approvals and system parameters.

**Key Specifications**:
- Multi-signature approval mechanism
- Manages committee member list
- Controls third-party approvals
- Sets system-wide parameters
- Implements time-locked changes

**Governance Functions**:
- `setThirdPartyReviewer`: Assign review permissions
- `reviewThirdParty`: Approve or reject applications
- `updateThirdPartyRules`: Modify compliance rules
- `setCommitteeMembers`: Manage governance participants

## Bridge System

### CollectionsBridgeRoot (Ethereum)

**Purpose**: Ethereum-side bridge contract for cross-chain collection transfers.

**Key Specifications**:
- Manages collection mapping between chains
- Implements deposit mechanism for bridging
- Validates and processes withdrawal claims
- Maintains chain synchronization state
- Integrates with Polygon bridge infrastructure

**Bridge Operations**:
- `depositFor`: Initiate transfer to Polygon
- `receiveMessage`: Process messages from Polygon
- Maps collections across chains
- Handles failed transfers gracefully

### CollectionsBridgeChild (Polygon)

**Purpose**: Polygon-side bridge contract for receiving and sending collections.

**Key Specifications**:
- Receives deposits from Ethereum
- Manages local collection representations
- Processes withdrawal requests
- Maintains collection metadata sync
- Implements emergency withdrawal mechanisms

**Child Bridge Features**:
- `deposit`: Receive assets from Ethereum
- `withdraw`: Initiate transfer to Ethereum
- `syncState`: Maintain chain consistency
- Emergency recovery functions

## Management Contracts

### CollectionManager

**Purpose**: Central management contract for collection operations and configuration.

**Key Specifications**:
- Centralizes collection management functions
- Implements batch operations for efficiency
- Manages global collection settings
- Provides administrative interfaces
- Tracks collection statistics

**Management Features**:
- Collection creation and configuration
- Batch item management
- Global parameter settings
- Emergency pause mechanisms
- Statistics and reporting

### Rarities

**Purpose**: Manages rarity tiers and pricing for wearable items.

**Key Specifications**:
- Defines rarity levels (Common, Uncommon, Rare, Epic, Legendary, Mythic, Unique)
- Sets maximum supply limits per rarity
- Implements oracle-based pricing
- Manages price updates and limits
- Provides rarity-based minting rules

**Rarity System**:
- Each rarity has defined supply limits
- Pricing varies by rarity tier
- Oracle integration for dynamic pricing
- Batch price updates supported
- Historical price tracking

### CollectionStore

**Purpose**: Marketplace contract for primary sales of collection items.

**Key Specifications**:
- Manages primary sales directly from collections
- Implements fee distribution system
- Supports multiple payment tokens
- Provides purchase validation
- Integrates with collection contracts

**Store Features**:
- `buy`: Purchase items from collections
- Fee calculation and distribution
- Stock management and limits
- Revenue tracking per collection
- Integration with external marketplaces

## Supporting Systems

### Meta-Transactions

**Purpose**: Enable gasless transactions for better user experience.

**Key Specifications**:
- Implements EIP-2771 for trusted forwarders
- Supports batched meta-transactions
- Validates signature and nonce
- Provides replay protection
- Integrates with all collection functions

### Programmatic Collections

**Purpose**: Support for programmatically generated collections.

**Key Specifications**:
- Allows algorithmic item generation
- Supports on-chain metadata generation
- Implements deterministic generation rules
- Provides seed-based randomization
- Enables dynamic collections

## Version History

### V1 (Legacy)
- Initial implementation with basic features
- Fixed rarity system
- Limited third-party support
- Basic bridge functionality

### V2 (Current)
- Enhanced item management system
- Improved third-party integration
- Advanced bridge capabilities
- Committee governance
- Meta-transaction support

### V3 (Future)
- Programmatic collection support
- Enhanced metadata system
- Improved gas optimization
- Advanced governance features
- Cross-chain interoperability

## Integration Points

### For Creators
- Deploy collections via factory
- Manage items and metadata
- Set pricing and distribution
- Track sales and revenue

### For Third Parties
- Register via ThirdPartyRegistry
- Purchase and manage slots
- Deploy approved collections
- Comply with platform rules

### For Users
- Mint wearables from collections
- Transfer across chains via bridge
- Trade on marketplaces
- Equip items on avatars