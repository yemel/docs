# Decentraland Technical Documentation

## Welcome

This documentation provides a comprehensive guide to Decentraland's smart contract ecosystem and technical infrastructure. Whether you're a developer building on Decentraland, a creator designing wearables, or a user wanting to understand the underlying technology, this documentation will help you navigate the decentralized virtual world's blockchain foundation.

## What is Decentraland?

Decentraland is a decentralized virtual world powered by the Ethereum blockchain. Users can create, experience, and monetize content and applications. The virtual world is divided into parcels of LAND, permanently owned digital real estate that users can build upon.

## Key Components

### 🪙 MANA Token
The native cryptocurrency that powers all economic transactions in Decentraland, from purchasing LAND to buying wearables and names.

### 🗺️ LAND & Estates
Virtual parcels represented as NFTs, each with unique coordinates in the world. Estates group multiple parcels for unified management.

### 👕 Wearables & Collectibles
Digital fashion and items for avatar customization, managed through a sophisticated collection system with rarity tiers.

### 🏪 Marketplaces
Evolution from on-chain order books to efficient signature-based trading, supporting various asset types and trade formats.

### 🏠 Rentals
System allowing LAND owners to lease their properties while maintaining ownership, enabling a vibrant builder economy.

## Quick Start

### For Developers
Start with the [Smart Contracts Overview](contracts/intro.md) to understand the architecture, then dive into specific contracts based on your integration needs.

### For Users
Learn about [MANA Token](contracts/mana.md) for understanding transactions, and [LAND Registry](contracts/land.md) for property ownership.

### For Creators
Explore the [Wearables System](contracts/wearables-contracts.md) to understand how to create and manage digital collectibles.

## Documentation Structure

```
📚 Documentation
├── 🏗️ Foundation Layer
│   ├── ERC721 Standard Implementation
│   └── MANA Token Economics
├── 🌍 Virtual World Infrastructure
│   ├── LAND Registry System
│   └── District Management
├── 🎨 Digital Assets
│   └── Wearables & Collections
├── 💰 Economic Systems
│   ├── Marketplace Evolution
│   └── Rental Mechanisms
└── 🔧 Supporting Infrastructure
    ├── Vesting Systems
    └── Administrative Tools
```

## Key Features

### Decentralized Ownership
- True ownership of digital assets via NFTs
- No central authority can revoke ownership
- Transparent on-chain records

### Interoperability
- Cross-chain bridges (Ethereum ↔ Polygon)
- Standard interfaces (ERC20, ERC721)
- Open APIs and protocols

### Economic Freedom
- Open marketplace for all assets
- Peer-to-peer transactions
- Creator royalties and revenues

### Governance
- Community-driven decisions
- DAO treasury management
- Transparent voting mechanisms

## Technical Stack

- **Blockchain**: Ethereum (L1) and Polygon (L2)
- **Standards**: ERC20, ERC721, EIP712, EIP1967
- **Languages**: Solidity for smart contracts
- **Tools**: OpenZeppelin, Hardhat, ethers.js

## Security

All smart contracts are:
- Open source and verifiable
- Audited by security firms
- Battle-tested in production
- Upgradeable with safeguards

## Getting Help

### Resources
- **GitHub**: [github.com/decentraland](https://github.com/decentraland)
- **Discord**: Join the developer community
- **Forum**: [forum.decentraland.org](https://forum.decentraland.org)

### Support Channels
- Technical questions: Discord #developers
- Bug reports: GitHub Issues
- Security: security@decentraland.org

## Contributing

Decentraland is open source and welcomes contributions:

1. **Code**: Submit PRs to relevant repositories
2. **Documentation**: Help improve these docs
3. **Testing**: Report bugs and issues
4. **Ideas**: Propose improvements in forums

## License

The Decentraland smart contracts are open source under various licenses. Check individual repositories for specific license information.

---

*This documentation is continuously updated. For the latest information, check the official Decentraland resources.*