# LAND Contracts

## Overview

The LAND contracts form the foundation of Decentraland's virtual world, managing the ownership and properties of virtual land parcels and estates. This system enables users to own, trade, and develop virtual real estate in a decentralized manner.

## Core Contracts

### LANDRegistry

**Purpose**: Main registry contract for all LAND parcels in Decentraland's virtual world.

**Key Specifications**:
- Each LAND is a unique NFT with coordinates (x, y)
- Implements full ERC721 standard for ownership
- Stores metadata for parcel content (scenes, experiences)
- Manages update operators for content deployment
- Supports estate creation from multiple parcels

**Coordinate System**:
- Cartesian coordinates with origin at (0, 0)
- Each parcel is 16m x 16m in the virtual world
- Coordinates range from -150 to 150 initially
- Expandable through governance decisions
- Unique token ID derived from coordinates

**Core Functions**:
- `assignNewParcel(x, y, beneficiary)`: Mint new LAND parcel
- `transferLand(x, y, to)`: Transfer using coordinates
- `updateLandData(x, y, data)`: Set parcel metadata
- `setUpdateOperator(tokenId, operator)`: Grant update permissions
- `encodeTokenId(x, y)`: Convert coordinates to token ID
- `decodeTokenId(tokenId)`: Extract coordinates from token ID

### EstateRegistry

**Purpose**: Registry for estates that group multiple LAND parcels under single ownership.

**Key Specifications**:
- Estates are ERC721 tokens containing multiple LANDs
- Maintains relationship between estates and parcels
- Supports dynamic addition/removal of parcels
- Preserves individual LAND identity within estates
- Enables bulk operations on multiple parcels

**Estate Management**:
- `createEstate(parcels, beneficiary)`: Create new estate from LANDs
- `addLandToEstate(estateId, parcels)`: Add parcels to existing estate
- `removeLandFromEstate(estateId, parcels, recipient)`: Remove parcels
- `transferLandFromEstate(estateId, parcel, recipient)`: Transfer single LAND
- `getEstateSize(estateId)`: Get number of parcels in estate

**Estate Features**:
- Single NFT representing multiple parcels
- Unified metadata for all parcels
- Shared update operator across estate
- Atomic operations on all parcels
- Fingerprint system for integrity

## Proxy Architecture

### LANDProxy

**Purpose**: Upgradeable proxy contract for LAND registry implementation.

**Key Specifications**:
- Implements transparent proxy pattern
- Separates logic from storage
- Enables contract upgrades without data migration
- Maintains consistent contract address
- Preserves all ownership data during upgrades

**Proxy Functions**:
- `upgrade(newImplementation)`: Update logic contract
- `delegatecall`: Forward calls to implementation
- Storage remains in proxy contract
- Admin-only upgrade capability
- Event emission for upgrade tracking

### EstateProxy

**Purpose**: Upgradeable proxy for Estate registry implementation.

**Key Specifications**:
- Similar architecture to LANDProxy
- Independent upgrade capability
- Maintains estate-LAND relationships
- Preserves all estate data
- Supports gradual feature rollout

## Storage Contracts

### LANDStorage

**Purpose**: Dedicated storage layout for LAND registry data.

**Storage Structure**:
- `latestPing`: Update tracking per parcel
- `authorizedDeploy`: Deployment permissions
- `landData`: Metadata storage
- `updateOperator`: Content update permissions
- `estateRegistry`: Link to estate contract

### EstateStorage

**Purpose**: Storage layout for estate registry data.

**Storage Structure**:
- `estateLandIds`: Parcels in each estate
- `landIdEstate`: Estate containing each parcel
- `estateData`: Metadata for estates
- `updateOperator`: Estate-wide operators
- `fingerprint`: Estate composition hash

## Metadata System

### LAND Metadata

**Purpose**: Store and manage content references for parcels.

**Metadata Format**:
```
{
  "version": 1,
  "name": "Parcel Name",
  "description": "Parcel Description",
  "ipns": "QmHash...",     // IPNS for dynamic content
  "ipfs": "QmHash...",     // IPFS for static content
  "https": "https://..."   // HTTPS endpoint
}
```

**Content Deployment**:
- Owner or operator can update metadata
- Multiple content delivery methods supported
- Version tracking for compatibility
- Off-chain content, on-chain reference
- Decentralized content hosting

### Estate Metadata

**Purpose**: Unified metadata for all parcels in an estate.

**Features**:
- Single metadata update for all parcels
- Consistent content across estate
- Shared scene deployment
- Bulk content management
- Version compatibility

## Update Operator System

### Operator Permissions

**Purpose**: Delegate content update rights without transferring ownership.

**Use Cases**:
- Grant builder access for development
- Enable rental agreements
- Allow collaborative building
- Temporary event management
- Maintenance delegation

**Operator Functions**:
- `setUpdateOperator(tokenId, operator)`: Grant permissions
- `setManyUpdateOperator(tokenIds[], operator)`: Bulk grant
- `getUpdateOperator(tokenId)`: Query current operator
- Operator can only update content, not transfer
- Revocable by owner at any time

## MinimeToken Integration

### MinimeToken

**Purpose**: Cloneable token contract used for governance snapshots.

**Integration Points**:
- Creates voting tokens based on LAND ownership
- Snapshot capability for governance votes
- Historical balance queries
- Fork capability for governance evolution
- No transfer capability (soulbound to LAND)

**Governance Features**:
- Voting weight based on LAND ownership
- Historical ownership verification
- Proposal snapshot mechanism
- Delegation capabilities
- Fork support for upgrades

## Authorization System

### Deployment Authorization

**Purpose**: Control who can deploy content to Decentraland.

**Authorization Levels**:
- **Owner**: Full rights to parcel
- **Update Operator**: Content deployment only
- **Authorized Deploy**: Legacy authorization system
- **Estate Manager**: Rights across entire estate

### Permission Hierarchy

1. **Owner Rights**:
   - Transfer ownership
   - Set update operator
   - Update metadata
   - Create/destroy estates
   - All other operations

2. **Operator Rights**:
   - Update metadata/content
   - Deploy scenes
   - Cannot transfer
   - Cannot change operator
   - Time-limited possible

3. **Viewer Rights**:
   - Read metadata
   - View ownership
   - Query coordinates
   - Access content
   - No modifications

## Coordinate Encoding

### Token ID Calculation

**Encoding Formula**:
```solidity
tokenId = uint256(
    keccak256(
        abi.encodePacked(x, y)
    )
)
```

**Properties**:
- Deterministic ID from coordinates
- Unique for each coordinate pair
- Irreversible (one-way hash)
- Collision-resistant
- Consistent across contracts

### Coordinate Recovery

**Decoding Process**:
- Maintain coordinate mapping
- Store x,y with token ID
- Query by either format
- Efficient lookups
- Gas-optimized storage

## Special Features

### Auction Integration

**LAND Auctions**:
- Initial distribution mechanism
- MANA burning for parcels
- Dutch auction format
- Coordinate selection
- Immediate ownership transfer

### Marketplace Integration

**Trading Features**:
- Direct ERC721 transfers
- Marketplace listings
- Auction support
- Bundle sales (estates)
- Rental agreements

### Builder Integration

**Development Tools**:
- Scene deployment APIs
- Metadata management
- Preview systems
- Collaborative building
- Version control

## Migration and Upgrades

### Upgrade Process

1. **Proposal Phase**:
   - Community proposal for upgrade
   - Technical specification
   - Security audit
   - Governance vote
   - Timelock execution

2. **Implementation**:
   - Deploy new logic contract
   - Test on testnet
   - Security verification
   - Proxy upgrade transaction
   - Verification period

### Data Preservation

**During Upgrades**:
- All ownership preserved
- Metadata maintained
- Operator settings kept
- Estate relationships intact
- No service interruption

## Security Considerations

### Access Control
- Owner-only sensitive functions
- Operator limitations
- Admin upgrade controls
- Timelock for changes
- Multi-sig requirements

### Data Integrity
- Coordinate validation
- Estate consistency checks
- Metadata format validation
- Fingerprint verification
- Ownership verification

### Economic Security
- No arbitrary minting
- Burn mechanism for auctions
- Transfer restrictions
- Fee mechanisms
- Anti-spam measures