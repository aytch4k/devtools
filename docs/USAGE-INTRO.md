# Using the Blockchain Development Environment

This guide provides detailed instructions for using the comprehensive blockchain development environment.

## Table of Contents

- [Getting Started](#getting-started) (this file)
- [Blockchain Development](./USAGE-BLOCKCHAIN.md)
- [Layer 2 Development](./USAGE-LAYER2.md)
- [Cross-Chain Development](./USAGE-CROSSCHAIN.md)
- [Smart Wallet Integration](./USAGE-WALLETS.md)
- [Frontend Development](./USAGE-FRONTEND.md)
- [Decentralized Storage](./USAGE-STORAGE.md)
- [Database Systems](./USAGE-DATABASES.md)
- [Advanced Cryptography](./USAGE-CRYPTO.md)
- [WebXR/AR/VR Development](./USAGE-WEBXR.md)
- [CI/CD Integration](./USAGE-CICD.md)

## Getting Started

### Prerequisites

- Docker and Docker Compose installed
- Git for version control
- At least 16GB RAM and 50GB disk space recommended

### Starting the Environment

```bash
# Clone the repository (if not already done)
git clone <repository-url>
cd <repository-directory>

# Start the development environment
cd devtools/docker
docker-compose up -d

# To start only specific services
docker-compose up -d dev-environment geth ipfs
```

### Accessing the Environment

- **VS Code Server**: http://localhost:3001
- **Next.js/React Apps**: http://localhost:3000
- **IPFS Gateway**: http://localhost:8081
- **Neo4j Browser**: http://localhost:7474
- **MongoDB**: mongodb://localhost:27017
- **PostgreSQL**: postgresql://localhost:5432
- **Milvus**: http://localhost:19530
- **Ethereum Node**: http://localhost:8545
- **Optimism Node**: http://localhost:8546
- **Arbitrum Node**: http://localhost:8547
- **zkSync Node**: http://localhost:3031
- **StarkNet Node**: http://localhost:5050
- **Polygon zkEVM Node**: http://localhost:8123

## Environment Setup

### Verifying Installed Tools

Open a terminal in the VS Code Server and run:

```bash
# Check versions of key tools
node -v                  # Node.js
npm -v                   # npm
go version               # Go
rustc --version          # Rust
solc --version           # Solidity
ipfs --version           # IPFS
hardhat --version        # Hardhat
cargo --version          # Cargo
dotnet --version         # .NET
python3 --version        # Python
psql --version           # PostgreSQL client
mongo --version          # MongoDB client
cypher-shell --version   # Neo4j client
```

### Directory Structure

The container is set up with the following structure:

- `/app/src` - Mount point for your source code
- `/app/config` - Configuration files
- `/app/go` - Go workspace
- `/app/.ipfs` - IPFS configuration
- `/app/.solana` - Solana configuration