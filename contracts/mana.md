# MANA Token Contract

## Overview

MANA is Decentraland's native ERC20 cryptocurrency token that powers the entire Decentraland economy. It serves as the primary medium of exchange for purchasing LAND, wearables, names, and other digital assets within the virtual world.

## Core Contracts

### MANAToken

**Purpose**: Main ERC20 token contract implementing MANA with additional features.

**Key Specifications**:
- Standard ERC20 implementation with 18 decimals
- Total supply determined by crowdsale and vesting
- Implements pausable functionality for emergency situations
- Supports token burning to reduce supply
- Includes mintable capability (restricted to crowdsale initially)

**Token Properties**:
- **Name**: "Decentraland MANA"
- **Symbol**: "MANA"
- **Decimals**: 18
- **Initial Supply**: Set during crowdsale
- **Maximum Supply**: No hard cap, but minting restricted

**Core Functions**:
- `transfer`: Send MANA to another address
- `approve`: Allow spender to transfer MANA
- `transferFrom`: Transfer MANA on behalf of owner
- `burn`: Permanently destroy MANA tokens
- `pause/unpause`: Emergency stop mechanism

### BurnableToken

**Purpose**: Extension allowing token holders to permanently destroy their tokens.

**Key Specifications**:
- Enables deflationary token mechanics
- Reduces total supply when tokens are burned
- Irreversible operation for economic finality
- Tracks burn events for transparency
- Used in LAND auctions and other sinks

**Burn Mechanics**:
- `burn(uint256 _value)`: Destroy specified amount of tokens
- Reduces caller's balance
- Decreases total supply
- Emits Burn event for tracking
- Cannot be reversed or recovered

## Crowdsale Contracts

### MANACrowdsale

**Purpose**: Initial token distribution mechanism through public crowdsale.

**Key Specifications**:
- Implements capped crowdsale with maximum raise limit
- Time-based sale with start and end blocks
- Variable pricing tiers based on time
- Whitelist support for preferential rates
- Automatic token distribution upon contribution

**Crowdsale Parameters**:
- **Duration**: Defined by block numbers
- **Cap**: Maximum ETH to be raised
- **Base Rate**: MANA per ETH
- **Bonus Structure**: Early bird bonuses
- **Minimum Contribution**: Set to prevent spam

**Distribution Model**:
- 40% allocated to crowdsale participants
- 20% reserved for foundation
- 20% allocated to team and early contributors
- 20% for community incentives and partnerships

### WhitelistedCrowdsale

**Purpose**: Crowdsale extension providing preferential terms for whitelisted addresses.

**Key Specifications**:
- Maintains whitelist of privileged buyers
- Offers better rates or guaranteed allocation
- Implements KYC/AML compliance mechanisms
- Supports batch whitelist operations
- Tracks whitelist participation separately

**Whitelist Features**:
- `addToWhitelist`: Add addresses to privileged list
- `removeFromWhitelist`: Revoke whitelist status
- Custom rates for whitelisted participants
- Reserved allocation pools
- Priority purchase windows

### MANAContinuousSale

**Purpose**: Ongoing token sale mechanism for continued distribution after initial crowdsale.

**Key Specifications**:
- Perpetual sale model with no end date
- Dynamic pricing based on demand
- Automatic rate adjustments
- Supports multiple payment methods
- Integrates with vesting schedules

**Continuous Sale Features**:
- No time limits on purchases
- Adjustable rates by governance
- Multiple currency support
- Vesting options for large purchases
- Revenue distribution to treasury

## Token Economics

### Supply Mechanics

**Initial Distribution**:
- Crowdsale participants: 40%
- Foundation reserve: 20%
- Team and contributors: 20%
- Community incentives: 20%

**Inflation/Deflation**:
- No ongoing inflation (minting restricted)
- Deflationary through burn mechanisms
- LAND purchases burn MANA
- Name claims burn MANA
- Transaction fees partially burned

### Utility Functions

**Primary Uses**:
1. **LAND Purchases**: Primary currency for land auctions
2. **Marketplace Transactions**: Buy/sell NFTs
3. **Name Registration**: Claim unique names
4. **Wearable Purchases**: Buy avatar items
5. **Service Payments**: Pay for various platform services

**Economic Sinks**:
- LAND auction burns
- Name registration burns
- Transaction fee burns
- Voluntary burns for voting weight
- Failed transaction penalties

## Security Features

### Pausable Mechanism

**Purpose**: Emergency stop functionality for critical situations.

**Implementation**:
- `pause()`: Stops all token transfers
- `unpause()`: Resumes normal operation
- Owner-only function
- Affects transfers but not views
- Event emission for transparency

**Use Cases**:
- Critical bug discovery
- Security breach response
- Regulatory compliance
- System maintenance
- Economic emergency

### Access Control

**Roles and Permissions**:
- **Owner**: Can pause/unpause, manage crowdsale
- **Minter**: Can create new tokens (restricted)
- **Whitelist Admin**: Manages crowdsale whitelist
- **User**: Standard transfer and burn rights

### Vesting Contracts

**Purpose**: Time-locked token distribution for team and advisors.

**Implementation**:
- Linear vesting over specified period
- Cliff periods before vesting starts
- Revocable and non-revocable grants
- Automatic release calculations
- Transfer restrictions during vesting

## Integration Points

### For Exchanges
- Standard ERC20 interface
- 18 decimal precision
- Pausable awareness required
- Burn event tracking
- High liquidity token

### For DApps
- Approve/transferFrom pattern
- Gas optimization for batch operations
- Burn integration for sinks
- Event monitoring for updates
- Meta-transaction support

### For Smart Contracts
- Safe transfer patterns
- Approval management
- Balance checking
- Burn capabilities
- Pause state awareness

## Historical Context

### Token Launch
- Launched in 2017 ICO
- Raised ~$24 million
- Initial price ~$0.024
- Ethereum-based ERC20
- Immediate exchange listings

### Major Milestones
1. **2017**: Token creation and ICO
2. **2018**: First LAND auction using MANA
3. **2019**: Marketplace launch with MANA
4. **2020**: Layer 2 integration planning
5. **2021**: Polygon deployment for scaling

## Technical Specifications

### Gas Optimization
- Efficient storage patterns
- Batch operation support
- Minimal external calls
- Optimized event emissions
- Reduced storage writes

### Standards Compliance
- Full ERC20 compliance
- OpenZeppelin base contracts
- Industry standard patterns
- Audited security measures
- Widely supported token

### Upgrade Path
- Non-upgradeable core token
- Governance can deploy new versions
- Migration mechanisms available
- Backwards compatibility maintained
- Bridge contracts for Layer 2