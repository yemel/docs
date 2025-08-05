# Pass-Through Contract

## Overview

The Pass-Through Contract is a specialized proxy system designed to provide controlled access to Estate Registry operations, primarily intended for District management in Decentraland. It acts as an intermediary layer that can selectively enable or disable specific functions while maintaining operational control.

## Core Contracts

### PassThrough

**Purpose**: Main proxy contract that forwards calls to the Estate Registry with configurable method filtering.

**Key Specifications**:
- Implements fallback function to forward calls to target contract
- Provides method-level access control through enable/disable mechanism
- Maintains operator permissions for authorized callers
- Implements time-based restrictions (up to 2 years)
- Preserves msg.sender context in forwarded calls

**Core Functionality**:
- `fallback`: Forwards allowed calls to Estate Registry
- Validates method signatures against disabled list
- Checks operator permissions before forwarding
- Returns target contract responses transparently
- Handles both successful and failed calls appropriately

**Security Features**:
- Method signature validation
- Operator-based access control
- Time-limited disable periods
- Owner-only configuration changes
- Reentrancy protection

### PassThroughManager

**Purpose**: Management contract for configuring pass-through rules and operators.

**Key Specifications**:
- Manages list of disabled methods
- Controls operator permissions
- Implements time-based disable logic
- Provides administrative interfaces
- Tracks configuration history

**Management Functions**:
- `disableMethod`: Block specific function calls
- `enableMethod`: Re-enable previously disabled methods
- `setOperator`: Grant operator permissions
- `removeOperator`: Revoke operator access
- `setMaxDisableTime`: Configure maximum disable duration

**Configuration Options**:
- Method-specific disable/enable
- Operator whitelist management
- Time limit enforcement (max 2 years)
- Batch configuration updates
- Emergency override capabilities

### PassThroughStorage

**Purpose**: Dedicated storage contract for pass-through configuration data.

**Key Specifications**:
- Separates storage from logic for upgradeability
- Stores disabled methods mapping
- Maintains operator permissions
- Tracks disable timestamps
- Preserves configuration history

**Storage Structure**:
- `disabledMethods`: Mapping of method signatures to disable status
- `operators`: Mapping of addresses to operator permissions
- `disableTimestamps`: Records when methods were disabled
- `targetContract`: Address of Estate Registry
- `owner`: Contract owner address

## Method Filtering System

### Default Disabled Methods

**Transfer Methods** (Disabled by default):
- `transferFrom(address,address,uint256)`: Standard ERC721 transfer
- `safeTransferFrom(address,address,uint256)`: Safe transfer without data
- `safeTransferFrom(address,address,uint256,bytes)`: Safe transfer with data

**Estate-Specific Methods** (Disabled by default):
- `transferLand(uint256,uint256,address)`: Transfer single LAND from estate
- `transferManyLands(uint256,uint256[],address)`: Transfer multiple LANDs
- `transferLandToEstate(uint256,uint256,uint256)`: Move LAND between estates
- `transferManyLandToEstate(uint256,uint256[],uint256)`: Batch LAND transfers

### Configurable Methods

**Update Operations** (Can be disabled/enabled):
- `updateMetadata`: Modify estate metadata
- `updateOperator`: Change estate operator
- `setUpdateOperator`: Set update permissions

**Administrative Functions** (Can be disabled/enabled):
- `addLandToEstate`: Add LAND parcels to estate
- `removeLandFromEstate`: Remove LAND from estate
- `setEstateData`: Update estate information

## Use Cases

### District Management

**Purpose**: Primary use case for managing Decentraland Districts.

**Implementation**:
- Districts use pass-through to control Estate Registry access
- Prevents unauthorized LAND transfers from district estates
- Allows selective enabling of management functions
- Maintains district integrity while enabling operations
- Supports community governance models

**Benefits**:
- Protects district LANDs from unauthorized transfers
- Enables controlled administrative access
- Supports time-limited operational windows
- Maintains audit trail of all operations

### Controlled Estate Access

**Purpose**: Provide limited access to estate operations for specific operators.

**Implementation**:
- Grant operator permissions for specific functions
- Disable dangerous operations permanently
- Enable safe operations as needed
- Implement time-boxed access windows
- Support multiple operator roles

### Security Layer

**Purpose**: Add additional security layer to Estate Registry operations.

**Implementation**:
- Disable high-risk functions by default
- Require explicit enabling for sensitive operations
- Implement time delays for critical changes
- Provide emergency disable mechanisms
- Maintain operation logs for audit

## Access Control Model

### Owner Privileges
- Configure disabled/enabled methods
- Manage operator permissions
- Set time limits for disables
- Transfer ownership
- Emergency overrides

### Operator Privileges
- Call enabled methods on Estate Registry
- Cannot modify pass-through configuration
- Cannot exceed time-limited permissions
- Subject to method filtering
- Tracked in all operations

### Public Access
- No direct access to Estate Registry
- Cannot call disabled methods
- Cannot bypass operator requirements
- Read-only access to configuration
- View current restrictions

## Time-Based Restrictions

### Maximum Disable Period
- Hard limit of 2 years (730 days)
- Prevents permanent lock-out
- Ensures eventual accessibility
- Configurable by owner
- Automatic re-enabling after expiry

### Disable Timestamps
- Recorded when method disabled
- Used to calculate expiry
- Immutable once set
- Publicly verifiable
- Reset on re-enable

## Integration Guidelines

### For District Administrators
1. Deploy pass-through pointing to Estate Registry
2. Transfer estate ownership to pass-through
3. Configure disabled methods for security
4. Grant operator permissions as needed
5. Monitor and adjust configuration

### For Operators
1. Receive operator permissions from owner
2. Call enabled methods through pass-through
3. Respect time-limited access windows
4. Follow operational guidelines
5. Report issues to administrators

### For Developers
1. Understand method filtering logic
2. Handle forwarded call responses
3. Implement proper error handling
4. Respect access control model
5. Test with various configurations

## Security Considerations

### Method Signature Validation
- Validates 4-byte function selectors
- Prevents unauthorized function calls
- Checks against disabled list
- Handles function overloading
- Maintains signature integrity

### Operator Management
- Whitelist-based access control
- Individual operator tracking
- Revocable permissions
- No delegation capabilities
- Audit trail maintenance

### Time Limit Enforcement
- Prevents permanent restrictions
- Automatic re-enabling
- Configurable limits
- Owner override capability
- Timestamp verification