# Blockchain Development Environment

A comprehensive development environment for full-stack, blockchain, web3, and dApp development.

## Overview

This development environment provides a complete setup for blockchain and web3 developers, supporting all major blockchains, frontend frameworks, and development tools. It's designed to be used as a containerized environment that can be easily deployed and shared across teams.

## Features

- **Blockchain Support**: Ethereum, Solana, Cosmos, Polkadot, Algorand, Cardano, Tezos, Avalanche, Near, Polygon, Hedera, and more
- **Layer 2 Solutions**: 
  - ZK Rollups (zkSync, StarkNet, Polygon zkEVM)
  - Optimistic Rollups (Optimism, Arbitrum)
  - State Channels and Sidechains
- **Cross-Chain Development**:
  - IBC (Inter-Blockchain Communication) for Cosmos
  - XCMP (Cross-Chain Message Passing) for Polkadot
  - Bridges (Wormhole, Axelar, LayerZero)
  - Parachains and App-chains
- **Smart Wallet Integration**:
  - MetaMask SDK
  - WalletConnect
  - Account Abstraction (EIP-4337)
  - Gnosis Safe
  - Trust Wallet Core
  - Biconomy Smart Accounts
- **Programming Languages**: JavaScript, TypeScript, Rust, Go, C, C++, Java EE, C#, Python, Haskell, WebAssembly
- **Frontend Frameworks**: Next.js, React, Vue.js, Angular, Three.js, A-Frame (WebXR/AR/VR)
- **Smart Contract Development**: Solidity, Rust, Move, Cairo, PyTeal, Ink!
- **Decentralized Storage**: IPFS, Filecoin, Arweave, Ceramic, OrbitDB
- **Database Systems**: 
  - PostgreSQL (SQL)
  - MongoDB (NoSQL)
  - Neo4j (Graph)
  - Milvus (Vector)
- **Advanced Cryptography**: 
  - Post-quantum (Kyber, Dilithium)
  - Fully Homomorphic Encryption
  - Multi-party Computation
  - Zero-Knowledge Proofs (SNARKs, STARKs)
  - Differential Privacy

## Architecture

The environment consists of:

1. A main development container with all necessary tools and libraries
2. Individual blockchain nodes for local development and testing
3. Layer 2 nodes (Optimism, Arbitrum, zkSync, StarkNet, Polygon zkEVM)
4. Cross-chain infrastructure (IBC Relayer, Axelar, Wormhole, LayerZero)
5. Database systems for various data storage needs
6. Supporting services (IPFS, Ceramic, etc.)
7. Persistent volumes for data storage

## Quick Start

```bash
# Start the development environment
cd devtools/docker
docker-compose up -d

# Access the VS Code Server
# Open http://localhost:3001 in your browser

# Access blockchain nodes
# Ethereum: http://localhost:8545
# Solana: http://localhost:8899
# IPFS Gateway: http://localhost:8081

# Access Layer 2 nodes
# Optimism: http://localhost:8546
# Arbitrum: http://localhost:8547
# zkSync: http://localhost:3031
# StarkNet: http://localhost:5050
# Polygon zkEVM: http://localhost:8123

# Access database systems
# PostgreSQL: postgresql://localhost:5432
# MongoDB: mongodb://localhost:27017
# Neo4j: http://localhost:7474 (Browser) / bolt://localhost:7687 (Connection)
# Milvus: http://localhost:19530
```

## CI/CD Integration

The included Jenkinsfile automates the build and deployment process, creating all necessary Docker images and volumes. This ensures consistent development environments across teams and CI/CD pipelines.

## Port Configuration

All services use non-standard ports to avoid conflicts with existing services:
- Development environment: 3000, 3001
- Blockchain nodes: Various non-conflicting ports
- Layer 2 nodes: Various non-conflicting ports
- Database systems: Standard ports (5432, 27017, 7474/7687, 19530)
- No services use ports 80, 8080, 8000, or 9000

## Layer 2 Development

### ZK Rollups
- **zkSync**: High-throughput, low gas cost Ethereum scaling solution using zero-knowledge proofs
- **StarkNet**: Permissionless decentralized ZK-Rollup using STARK proofs
- **Polygon zkEVM**: ZK-Rollup that's fully compatible with existing Ethereum smart contracts

### Optimistic Rollups
- **Optimism**: EVM-equivalent scaling solution using optimistic rollups
- **Arbitrum**: Scaling solution with advanced fraud proofs and EVM compatibility

## Cross-Chain Development

### Cosmos Ecosystem (IBC)
- Full IBC (Inter-Blockchain Communication) protocol support
- Relayer configuration and management
- Cosmos SDK for app-chain development

### Polkadot Ecosystem (XCMP)
- Parachain development tools
- XCMP (Cross-Chain Message Passing) implementation
- Substrate framework for blockchain creation

### Bridge Technologies
- **Wormhole**: Generic message passing protocol
- **Axelar**: Secure cross-chain communication
- **LayerZero**: Trustless omnichain interoperability protocol

## Smart Wallet Integration

### Account Abstraction (EIP-4337)
- Smart contract wallets with advanced features
- Gasless transactions and batched operations
- Social recovery and multi-signature support

### Wallet SDKs
- **MetaMask SDK**: Connect dApps to MetaMask
- **WalletConnect**: Open protocol for connecting wallets to dApps
- **Trust Wallet Core**: Cross-platform wallet library

## Database Systems

### PostgreSQL (SQL)
- Relational database for structured data
- Ideal for transaction-heavy applications
- Supports complex queries and joins
- ACID compliant

### MongoDB (NoSQL)
- Document-oriented database
- Flexible schema for unstructured data
- High performance for read/write operations
- Horizontal scaling

### Neo4j (Graph)
- Native graph database
- Optimized for connected data
- Cypher query language
- Ideal for relationship-rich data

### Milvus (Vector)
- Vector database for similarity search
- AI and machine learning applications
- Efficient nearest neighbor search
- Supports high-dimensional vectors

## For More Information

See the [USAGE.md](./USAGE.md) file for detailed usage instructions and examples.
