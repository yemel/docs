# Vestings Builder

## Overview

The Vestings Builder repository provides a comprehensive token vesting system for Decentraland, supporting both simple linear vesting and complex periodic vesting schedules. It includes deployment tools for batch vesting creation and uses gas-efficient proxy patterns to minimize deployment costs.

## Core Vesting Contracts

### PeriodicTokenVesting

**Purpose**: Advanced vesting contract supporting complex, period-based token release schedules with optional linear interpolation.

**Key Specifications**:
- Supports multiple vesting periods with different amounts
- Optional linear interpolation within periods for smooth release
- Revocable and pausable functionality
- Cliff period before first release
- Beneficiary transfer capabilities
- Surplus token management

**Vesting Configuration**:
```solidity
struct VestingSchedule {
    address beneficiary;           // Who receives tokens
    IERC20 token;                 // Token being vested
    bool revocable;               // Can be revoked by owner
    bool pausable;                // Can be paused temporarily
    uint256 start;                // Vesting start timestamp
    uint256 cliff;                // Cliff period duration
    uint256[] periodDurations;   // Duration of each period
    uint256[] periodAmounts;     // Tokens per period
    bool linearlyInterpolated;   // Smooth vs step release
}
```

**Core Functions**:
- `initialize(token, beneficiary, revocable, pausable, start, cliff, periods, amounts, linear)`: Set up vesting
- `release()`: Claim vested tokens
- `revoke()`: Owner cancels vesting and recovers unvested tokens
- `pause()/unpause()`: Temporarily halt vesting
- `getVested()`: Calculate currently vested amount
- `getReleasable()`: Get claimable tokens
- `releaseSurplus()`: Withdraw excess tokens

**Period-Based Release**:

The contract supports flexible release schedules:

1. **Step Function Release** (linearlyInterpolated = false):
   - Tokens released in discrete chunks at period ends
   - Example: 1000 tokens at month 3, 1000 at month 6, etc.

2. **Linear Release Within Periods** (linearlyInterpolated = true):
   - Smooth token release throughout each period
   - Example: Daily release of tokens within each period

**Example Vesting Schedules**:

```solidity
// Quarterly vesting over 2 years
periodDurations = [3 months, 3 months, 3 months, 3 months, 3 months, 3 months, 3 months, 3 months]
periodAmounts = [12500, 12500, 12500, 12500, 12500, 12500, 12500, 12500]
// Total: 100,000 tokens over 8 quarters

// Front-loaded vesting
periodDurations = [1 month, 2 months, 3 months, 6 months]
periodAmounts = [40000, 30000, 20000, 10000]
// More tokens released early
```

### Legacy TokenVesting (VestingImpl)

**Purpose**: Simple linear vesting implementation for straightforward vesting needs.

**Key Specifications**:
- Linear token release over time
- Single cliff period support
- Revocable by owner
- Beneficiary management
- Standard vesting pattern

**Core Functions**:
- `initialize(token, beneficiary, start, cliff, duration, revocable)`: Set up linear vesting
- `release()`: Claim vested tokens
- `revoke()`: Cancel and recover unvested
- `changeBeneficiary(newBeneficiary)`: Transfer vesting rights
- `getVested()`: Calculate vested amount

**Linear Vesting Formula**:
```
vestedAmount = totalAmount * (currentTime - startTime) / vestingDuration
```

## Deployment Infrastructure

### MinimalProxyFactory

**Purpose**: Gas-efficient factory for deploying vesting contracts using EIP-1167 minimal proxy pattern.

**Key Specifications**:
- Uses CREATE2 for deterministic addresses
- Deploys minimal proxy contracts (45 bytes)
- Reduces deployment gas by ~10x
- Automatic initialization after deployment
- Salt-based address generation

**Proxy Pattern Benefits**:
- Implementation deployed once
- Each vesting is a minimal proxy
- Massive gas savings for multiple vestings
- Predictable addresses
- Upgradeable implementation

**Core Functions**:
- `createProxy(implementation, salt, initData)`: Deploy and initialize proxy
- `computeAddress(implementation, salt)`: Calculate deployment address
- Deployment cost: ~50,000 gas vs ~500,000 for full contract

### OwnableBatchVestings

**Purpose**: Batch deployment utility for creating multiple vesting contracts in a single transaction.

**Key Specifications**:
- Deploy multiple vestings atomically
- Owner-controlled deployment
- Uses factory for gas efficiency
- Configurable initialization parameters
- Event emission for tracking

**Batch Deployment Structure**:
```solidity
struct VestingData {
    address beneficiary;
    uint256 amount;
    uint256 start;
    uint256 cliff;
    uint256[] periods;
    uint256[] amounts;
    bool revocable;
    bool pausable;
    bool linear;
}

function createVestings(VestingData[] vestings) external onlyOwner {
    // Deploys all vestings in single transaction
}
```

**Use Cases**:
- Team token distribution
- Investor vesting setup
- Advisor allocations
- Community rewards
- Airdrop vesting

### BatchVestings (Legacy)

**Purpose**: Original batch deployment contract for simple linear vestings.

**Key Specifications**:
- Creates multiple linear vestings
- Single transaction deployment
- Uniform or custom parameters
- Gas optimization through batching

## Vesting Management Features

### Revocability

**Purpose**: Allow vesting contracts to be cancelled with unvested token recovery.

**Implementation**:
- Owner can revoke at any time
- Released tokens stay with beneficiary
- Unvested tokens return to owner
- Irreversible once revoked
- Event emission for transparency

**Revocation Scenarios**:
- Employee termination
- Contract violations
- Agreement changes
- Emergency recovery

### Pausability

**Purpose**: Temporarily halt vesting without cancellation.

**Implementation**:
- Owner can pause/unpause
- Vesting calculation frozen during pause
- No token release while paused
- Preserves vesting schedule
- Useful for dispute resolution

**Pause Use Cases**:
- Compliance holds
- Investigation periods
- Temporary restrictions
- Agreement negotiations

### Beneficiary Management

**Transfer Rights**:
- Current beneficiary can transfer rights
- New beneficiary receives future releases
- Already released tokens unaffected
- Single ownership at any time
- Event emission for tracking

**Transfer Scenarios**:
- Estate planning
- Account migration
- Company acquisitions
- Legal transfers

### Surplus Management

**Purpose**: Handle tokens sent to vesting contract beyond vesting amount.

**Features**:
- Detect surplus tokens
- Owner can withdraw excess
- Protects vesting amount
- Handles accidental transfers
- Foreign token recovery

**Surplus Scenarios**:
- Accidental over-funding
- Dividend distributions
- Airdrop receipts
- Migration remainders

## Security Features

### Access Control
- Owner-only administrative functions
- Beneficiary-only claims
- No unauthorized access
- Clear role separation

### Reentrancy Protection
- State updates before transfers
- Safe transfer patterns
- No external calls in critical sections
- OpenZeppelin SafeERC20

### Input Validation
- Parameter sanity checks
- Overflow protection
- Zero address checks
- Schedule validation

### Emergency Recovery
- Revocation capability
- Pause mechanism
- Surplus withdrawal
- Foreign token recovery

## Gas Optimization Strategies

### Proxy Pattern Savings

**Traditional Deployment**:
- Full contract: ~2,000,000 gas
- 100 vestings: ~200,000,000 gas
- High deployment costs

**Proxy Pattern Deployment**:
- Implementation: ~2,000,000 gas (once)
- Per proxy: ~50,000 gas
- 100 vestings: ~7,000,000 gas total
- 96% gas savings

### Batch Operations
- Single transaction for multiple vestings
- Reduced overhead costs
- Atomic deployment
- Efficient initialization

### Storage Optimization
- Packed structs where possible
- Minimal storage updates
- Efficient data structures
- Reused implementation storage

## Integration Examples

### Creating a Simple Linear Vesting

```solidity
// Deploy vesting for team member
VestingFactory.createVesting(
    token: MANA_ADDRESS,
    beneficiary: teamMemberAddress,
    amount: 100000e18,
    start: block.timestamp,
    cliff: 365 days,
    duration: 4 years,
    revocable: true
);
```

### Creating Complex Periodic Vesting

```solidity
// Quarterly vesting with increasing amounts
PeriodicTokenVesting.initialize(
    token: MANA_ADDRESS,
    beneficiary: investorAddress,
    revocable: false,
    pausable: true,
    start: block.timestamp,
    cliff: 90 days,
    periodDurations: [90 days, 90 days, 90 days, 90 days],
    periodAmounts: [10000e18, 20000e18, 30000e18, 40000e18],
    linearlyInterpolated: true
);
```

### Batch Vesting Deployment

```solidity
// Deploy vestings for entire team
VestingData[] memory vestings = new VestingData[](teamSize);
for (uint i = 0; i < teamSize; i++) {
    vestings[i] = VestingData({
        beneficiary: teamMembers[i],
        amount: allocations[i],
        start: startDate,
        cliff: 6 months,
        duration: 3 years,
        revocable: true
    });
}
BatchVestings.createVestings(vestings);
```

## Use Case Scenarios

### Employee Compensation
- 4-year vesting with 1-year cliff
- Monthly or quarterly release
- Revocable upon termination
- Transfer for account changes

### Investor Allocations
- Custom vesting schedules
- Non-revocable agreements
- Periodic or linear release
- Long-term alignment

### Community Rewards
- Gradual token distribution
- Non-revocable grants
- Encourages long-term participation
- Fair distribution mechanism

### Ecosystem Grants
- Milestone-based release
- Pausable for compliance
- Performance-linked vesting
- Project funding

## Monitoring and Management

### Events
- `TokensReleased`: Successful claim
- `VestingRevoked`: Cancellation
- `VestingPaused/Unpaused`: Status change
- `BeneficiaryChanged`: Transfer
- `SurplusWithdrawn`: Excess recovery

### View Functions
- `getVested()`: Calculate vested amount
- `getReleasable()`: Available to claim
- `getVestingInfo()`: Full schedule details
- `isPaused()`: Current status
- `isRevoked()`: Revocation status

### Management Interface
- Owner dashboard for oversight
- Beneficiary claim interface
- Schedule visualization
- Transaction history
- Status monitoring

## Best Practices

### Deployment
1. Test vesting parameters thoroughly
2. Verify token approval/transfer
3. Use proxy pattern for gas savings
4. Batch similar vestings
5. Document vesting terms

### Security
1. Audit vesting parameters
2. Test revocation scenarios
3. Implement access controls
4. Monitor for anomalies
5. Have emergency procedures

### Operations
1. Regular claim reminders
2. Clear documentation
3. Support for beneficiaries
4. Tracking and reporting
5. Compliance procedures