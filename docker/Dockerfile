# Use a multi-stage build for efficiency
FROM ubuntu:22.04 AS builder

# Install basic dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    curl \
    git \
    unzip \
    wget \
    python3 \
    python3-pip \
    openjdk-17-jdk \
    maven \
    gradle \
    cmake \
    clang \
    libssl-dev \
    pkg-config \
    software-properties-common \
    apt-transport-https \
    ca-certificates \
    gnupg \
    lsb-release \
    && rm -rf /var/lib/apt/lists/*

# Install Go (for Cosmos, Avalanche, backend)
ENV GO_VERSION=1.22.1
RUN wget https://golang.org/dl/go${GO_VERSION}.linux-amd64.tar.gz \
    && tar -C /usr/local -xzf go${GO_VERSION}.linux-amd64.tar.gz \
    && rm go${GO_VERSION}.linux-amd64.tar.gz
ENV PATH="/usr/local/go/bin:${PATH}"

# Install Rust (for Solana, Polkadot, WASM)
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
RUN /root/.cargo/bin/rustup target add wasm32-unknown-unknown
RUN /root/.cargo/bin/rustup component add rust-src
ENV PATH="/root/.cargo/bin:${PATH}"

# Install Node.js and npm (for Next.js, React, Ethereum, Web3)
ENV NODE_VERSION=20
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm

# Install Yarn
RUN npm install -g yarn

# Install Next.js, React, and modern frontend frameworks
RUN npm install -g create-next-app create-react-app @vue/cli vite

# Install Three.js, A-Frame, and other WebXR/WebGL frameworks
RUN mkdir -p /opt/web3d && cd /opt/web3d \
    && npm init -y \
    && npm install three aframe aframe-ar aframe-particle-system-component \
    react-three-fiber @react-three/drei @react-three/xr \
    babylon.js @babylonjs/core @babylonjs/loaders @babylonjs/materials \
    playcanvas engine-playcanvas

# Install Solidity (solc) for Ethereum, Binance Smart Chain, Polygon
RUN npm install -g solc@0.8.20

# Install Ethereum development tools
RUN npm install -g hardhat truffle ganache @openzeppelin/contracts ethers web3 \
    @truffle/hdwallet-provider @nomiclabs/hardhat-ethers @nomiclabs/hardhat-waffle \
    @nomiclabs/hardhat-etherscan

# ===== Smart Wallet Libraries and Tools =====

# Install Web3 wallet libraries
RUN npm install -g \
    @metamask/sdk \
    @metamask/providers \
    @walletconnect/web3-provider \
    @walletconnect/client \
    @walletconnect/qrcode-modal \
    web3modal \
    @coinbase/wallet-sdk \
    @ledgerhq/hw-app-eth \
    @ledgerhq/hw-transport-webusb \
    @torus-io/torus-embed \
    @gnosis.pm/safe-core-sdk \
    @gnosis.pm/safe-ethers-lib \
    @gnosis.pm/safe-service-client \
    @biconomy/mexa \
    @biconomy/smart-account \
    @biconomy/core-types \
    @biconomy/common \
    @biconomy/bundler \
    @biconomy/paymaster \
    @account-abstraction/sdk \
    @account-abstraction/contracts \
    @thirdweb-dev/wallets \
    @thirdweb-dev/auth \
    @thirdweb-dev/sdk

# Install Trust Wallet Core
RUN git clone https://github.com/trustwallet/wallet-core.git /opt/wallet-core \
    && cd /opt/wallet-core \
    && tools/install-dependencies

# Install EIP-4337 Account Abstraction tools
RUN npm install -g \
    @eth-infinitism/account-abstraction \
    @eth-infinitism/bundler \
    @zerodevapp/sdk \
    permissionless

# ===== Layer 2 Development Tools =====

# Install ZK Rollup tools
RUN npm install -g \
    @matter-labs/hardhat-zksync \
    @matterlabs/zksync-cli \
    zksync-web3 \
    @arbitrum/sdk \
    @optimism/sdk \
    @starkware-industries/starkware-crypto-utils \
    @starkware-industries/starkex-js \
    @starkware-industries/starknet-hardhat-plugin \
    @aztec/bridge-clients \
    @aztec/sdk \
    @loopring-web/loopring-sdk

# Install ZK-SNARKs and ZK-STARKs tools
RUN npm install -g \
    snarkjs \
    circom \
    circomlib \
    @noir-lang/noir_wasm \
    @noir-lang/aztec_backend \
    @starkware-industries/cairo-lang \
    @starkware-industries/starknet.js

# Install Cairo for StarkNet
RUN curl -L https://github.com/franalgaba/cairo-installer/raw/main/bin/cairo-installer | bash

# Install Polygon zkEVM tools
RUN npm install -g \
    @maticnetwork/maticjs-zkevm \
    @maticnetwork/zkevm-node \
    @0xpolygonhermez/zkevm-commonjs

# Install Optimism tools
RUN npm install -g \
    @eth-optimism/sdk \
    @eth-optimism/core-utils \
    @eth-optimism/contracts

# Install Arbitrum tools
RUN npm install -g \
    @arbitrum/sdk \
    @arbitrum/token-bridge-sdk

# ===== Cross-Chain Development Tools =====

# Install IBC (Inter-Blockchain Communication) tools
RUN go install github.com/cosmos/ibc-go/modules/core@latest
RUN go install github.com/cosmos/relayer/cmd/rly@latest

# Install Polkadot XCMP (Cross-Chain Message Passing) tools
RUN cargo install --git https://github.com/paritytech/cumulus xcm-emulator
RUN cargo install --git https://github.com/paritytech/polkadot-sdk subxt-cli

# Install Parachain development tools
RUN cargo install --git https://github.com/paritytech/substrate-parachain-template

# Install bridge development tools
RUN npm install -g \
    @chainsafe/chainbridge-core \
    @chainsafe/chainbridge-contracts \
    @axelar-network/axelarjs-sdk \
    @layerzerolabs/scan-client \
    @layerzerolabs/lz-sdk \
    @layerzerolabs/ui-kit \
    @wormhole-foundation/sdk \
    @wormhole-foundation/wormhole-cli \
    @connext/nxtp-sdk \
    @connext/nxtp-utils \
    @socket.tech/socket-v2-sdk \
    @socket.tech/dl-core

# Install Cosmos SDK and app-chain development tools
RUN go install github.com/cosmos/cosmos-sdk/cmd/ignite@latest
RUN go install github.com/tendermint/tendermint/cmd/tendermint@latest
RUN go install github.com/cosmos/gaia/cmd/gaiad@latest

# Install Avalanche Subnet tools
RUN go install github.com/ava-labs/avalanche-cli@latest

# Install .NET Core (C# for backend)
ENV DOTNET_VERSION=8.0
RUN wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh \
    && chmod +x dotnet-install.sh \
    && ./dotnet-install.sh --version latest \
    && rm dotnet-install.sh
ENV PATH="/root/.dotnet:${PATH}"
ENV DOTNET_ROOT="/root/.dotnet"

# Install C/C++ tools
RUN apt-get update && apt-get install -y \
    gcc \
    g++ \
    gdb \
    make \
    ninja-build \
    && rm -rf /var/lib/apt/lists/*

# Install WebAssembly tools (WASM, WebGL, WebXR)
RUN curl -sSL https://github.com/WebAssembly/wabt/releases/download/1.0.34/wabt-1.0.34-ubuntu.tar.gz -o wabt.tar.gz \
    && tar -xzf wabt.tar.gz -C /usr/local --strip-components=1 \
    && rm wabt.tar.gz

# Install Emscripten (C/C++ to WASM)
RUN git clone https://github.com/emscripten-core/emsdk.git /opt/emsdk \
    && cd /opt/emsdk \
    && ./emsdk install latest \
    && ./emsdk activate latest
ENV PATH="/opt/emsdk:/opt/emsdk/upstream/emscripten:${PATH}"

# Install Haskell (for Cardano)
RUN curl -sSL https://get.haskellstack.org/ | sh

# Install Tezos tools
RUN curl -sSL https://github.com/tezos/tezos/releases/download/v19.1/tezos-v19.1-x86_64.tar.gz -o tezos.tar.gz \
    && tar -xzf tezos.tar.gz -C /usr/local/bin \
    && rm tezos.tar.gz

# Install Solana CLI
RUN sh -c "$(curl -sSfL https://release.solana.com/v1.18.4/install)"

# Install Algorand tools (PyTEAL)
RUN pip3 install pyteal py-algorand-sdk

# Install Polkadot tools
RUN cargo install subkey --force

# Install Avalanche tools
RUN go install github.com/ava-labs/avalanchego/cmd/avalanchego@latest

# Install Cosmos SDK tools and IBC
RUN go install github.com/cosmos/cosmos-sdk/cmd/ignite@latest
RUN go install github.com/cosmos/ibc-go/modules/core@latest
RUN go install github.com/cosmos/relayer/cmd/rly@latest
RUN go install github.com/cosmos/gaia/cmd/gaiad@latest

# Install Near Protocol tools
RUN npm install -g near-cli

# Install Docker CLI (for Docker-in-Docker operations)
RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg \
    && echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
    > /etc/apt/sources.list.d/docker.list \
    && apt-get update && apt-get install -y docker-ce-cli \
    && rm -rf /var/lib/apt/lists/*

# Install modern CSS frameworks and tools
RUN npm install -g sass less stylus postcss-cli autoprefixer tailwindcss \
    @tailwindcss/forms @tailwindcss/typography @tailwindcss/aspect-ratio

# Install VS Code Server (optional IDE in container)
RUN curl -fsSL https://code-server.dev/install.sh | sh

# Install Java EE tools
RUN apt-get update && apt-get install -y \
    tomcat9 \
    && rm -rf /var/lib/apt/lists/*

# Install Python ML/AI libraries for data visualization
RUN pip3 install numpy pandas matplotlib seaborn plotly scikit-learn tensorflow torch

# ===== Post-Quantum Cryptography (PQC) and Advanced Cryptography Tools =====

# Install OpenSSL 3 (includes some PQC algorithms)
RUN apt-get update && apt-get install -y \
    libssl-dev \
    && rm -rf /var/lib/apt/lists/*

# Install NIST PQC libraries (Kyber, Dilithium, etc.)
RUN git clone https://github.com/pq-crystals/kyber.git /opt/kyber \
    && cd /opt/kyber && make

RUN git clone https://github.com/pq-crystals/dilithium.git /opt/dilithium \
    && cd /opt/dilithium && make

# Install liboqs (Open Quantum Safe) for post-quantum cryptography
RUN apt-get update && apt-get install -y \
    astyle \
    cmake \
    gcc \
    ninja-build \
    libssl-dev \
    python3-pytest \
    python3-pytest-xdist \
    unzip \
    xsltproc \
    doxygen \
    graphviz \
    && rm -rf /var/lib/apt/lists/*

RUN git clone --depth 1 https://github.com/open-quantum-safe/liboqs.git /opt/liboqs \
    && cd /opt/liboqs \
    && mkdir build && cd build \
    && cmake -GNinja -DCMAKE_INSTALL_PREFIX=/usr/local .. \
    && ninja && ninja install

# Install OQS-OpenSSL for PQC TLS
RUN git clone --depth 1 --branch OQS-OpenSSL_1_1_1-stable https://github.com/open-quantum-safe/openssl.git /opt/oqs-openssl \
    && cd /opt/oqs-openssl \
    && ./Configure no-shared linux-x86_64 -lm \
    && make -j$(nproc)

# Install Merkle tree libraries
RUN pip3 install merkletools pymerkle merkly
RUN npm install -g merkletreejs

# Install Fully Homomorphic Encryption (FHE) libraries
RUN git clone https://github.com/microsoft/SEAL.git /opt/SEAL \
    && cd /opt/SEAL \
    && cmake -S . -B build -DSEAL_USE_MSGSL=OFF -DSEAL_USE_ZLIB=OFF \
    && cmake --build build \
    && cmake --install build

# Install HElib (another FHE library)
RUN git clone https://github.com/homenc/HElib.git /opt/HElib \
    && cd /opt/HElib \
    && mkdir build && cd build \
    && cmake .. \
    && make -j$(nproc) \
    && make install

# Install TFHE (Fast Fully Homomorphic Encryption Library)
RUN git clone https://github.com/tfhe/tfhe.git /opt/tfhe \
    && cd /opt/tfhe \
    && mkdir build && cd build \
    && cmake ../src \
    && make -j$(nproc) \
    && make install

# Install Multi-Party Computation (MPC) libraries
RUN git clone https://github.com/data61/MP-SPDZ.git /opt/MP-SPDZ \
    && cd /opt/MP-SPDZ \
    && make -j$(nproc) setup

# Install ZK-SNARKs libraries
RUN git clone https://github.com/scipr-lab/libsnark.git /opt/libsnark \
    && cd /opt/libsnark \
    && mkdir build && cd build \
    && cmake .. \
    && make -j$(nproc)

# Install ZoKrates (ZK-SNARKs toolbox)
RUN curl -LSfs get.zokrat.es | sh

# Install Differential Privacy libraries
RUN pip3 install diffprivlib pydp opendp-smartnoise

# Install Google's Differential Privacy library
RUN git clone https://github.com/google/differential-privacy.git /opt/differential-privacy \
    && cd /opt/differential-privacy \
    && bazel build cc/...

# Install secure multi-party computation frameworks
RUN pip3 install PySyft crypten

# Install additional cryptographic libraries
RUN pip3 install cryptography pycryptodome pyopenssl pynacl

# Install zkSync development tools
RUN npm install -g zksync-cli

# Install StarkNet tools
RUN pip3 install starknet-py cairo-lang

# ===== Storage Technologies =====

# Install IPFS (full installation)
RUN wget https://dist.ipfs.tech/kubo/v0.26.0/kubo_v0.26.0_linux-amd64.tar.gz \
    && tar -xvzf kubo_v0.26.0_linux-amd64.tar.gz \
    && cd kubo \
    && bash install.sh \
    && rm -rf ../kubo_v0.26.0_linux-amd64.tar.gz ../kubo

# Install IPFS Cluster
RUN wget https://dist.ipfs.tech/ipfs-cluster-service/v1.0.6/ipfs-cluster-service_v1.0.6_linux-amd64.tar.gz \
    && tar -xvzf ipfs-cluster-service_v1.0.6_linux-amd64.tar.gz \
    && mv ipfs-cluster-service/ipfs-cluster-service /usr/local/bin/ \
    && rm -rf ipfs-cluster-service_v1.0.6_linux-amd64.tar.gz ipfs-cluster-service

# Install IPFS Companion tools
RUN npm install -g ipfs-http-client ipfs-core ipfs-only-hash orbit-db

# Install Filecoin tools
RUN wget https://github.com/filecoin-project/lotus/releases/download/v1.25.0/lotus_1.25.0_linux-amd64.tar.gz \
    && tar -xvzf lotus_1.25.0_linux-amd64.tar.gz \
    && mv lotus lotus-miner lotus-worker /usr/local/bin/ \
    && rm lotus_1.25.0_linux-amd64.tar.gz

# Install Filecoin development libraries
RUN npm install -g @glif/filecoin-address @glif/filecoin-number

# Install Arweave tools and libraries
RUN npm install -g arweave arlocal arweave-deploy
RUN npm install -g graphql-request graphql

# Install The Graph Protocol tools
RUN npm install -g @graphprotocol/graph-cli @graphprotocol/graph-ts

# Install Hedera Hashgraph SDK
RUN npm install -g @hashgraph/sdk
RUN pip3 install hedera-sdk-py

# Install Polygon (Matic) development tools
RUN npm install -g @maticnetwork/maticjs @maticnetwork/maticjs-web3 @maticnetwork/maticjs-ethers

# Install Ceramic Network tools
RUN npm install -g @ceramicnetwork/cli @ceramicnetwork/http-client @ceramicnetwork/3id-did-resolver

# Install DID (Decentralized Identity) tools
RUN npm install -g did-resolver did-jwt did-jwt-vc ethr-did-resolver web-did-resolver key-did-resolver

# Install Textile/ThreadDB tools
RUN npm install -g @textile/hub @textile/threads-id @textile/threads-client

# Install OrbitDB (IPFS-based databases)
RUN npm install -g orbit-db ipfs-log

# ===== Database Systems =====

# Install PostgreSQL client
RUN apt-get update && apt-get install -y \
    postgresql-client \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Install MongoDB client
RUN wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | apt-key add - \
    && echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | tee /etc/apt/sources.list.d/mongodb-org-6.0.list \
    && apt-get update && apt-get install -y \
    mongodb-org-shell \
    mongodb-org-tools \
    && rm -rf /var/lib/apt/lists/*

# Install Neo4j client
RUN wget -O - https://debian.neo4j.com/neotechnology.gpg.key | apt-key add - \
    && echo 'deb https://debian.neo4j.com stable latest' | tee /etc/apt/sources.list.d/neo4j.list \
    && apt-get update && apt-get install -y \
    cypher-shell \
    && rm -rf /var/lib/apt/lists/*

# Install Milvus client
RUN pip3 install pymilvus

# Install database drivers and ORMs
RUN pip3 install \
    psycopg2-binary \
    sqlalchemy \
    pymongo \
    motor \
    neo4j \
    py2neo \
    redis \
    pymilvus

RUN npm install -g \
    pg \
    sequelize \
    sequelize-cli \
    mongoose \
    neo4j-driver \
    redis \
    typeorm \
    prisma \
    @prisma/client

# Final image
FROM ubuntu:22.04

# Copy all installed tools from builder
COPY --from=builder /usr/local /usr/local
COPY --from=builder /root/.cargo /root/.cargo
COPY --from=builder /root/.dotnet /root/.dotnet
COPY --from=builder /opt /opt
COPY --from=builder /usr/bin/python3 /usr/bin/python3
COPY --from=builder /usr/lib/python3 /usr/lib/python3
COPY --from=builder /usr/local/lib/python3.10 /usr/local/lib/python3.10
COPY --from=builder /usr/local/bin/code-server /usr/local/bin/code-server
COPY --from=builder /usr/bin/docker /usr/bin/docker
COPY --from=builder /usr/bin/mongo /usr/bin/mongo
COPY --from=builder /usr/bin/mongosh /usr/bin/mongosh
COPY --from=builder /usr/bin/mongodump /usr/bin/mongodump
COPY --from=builder /usr/bin/mongorestore /usr/bin/mongorestore
COPY --from=builder /usr/bin/psql /usr/bin/psql
COPY --from=builder /usr/bin/cypher-shell /usr/bin/cypher-shell
COPY --from=builder /root/.local /root/.local

# Install runtime dependencies
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    libssl-dev \
    pkg-config \
    git \
    curl \
    wget \
    unzip \
    vim \
    nano \
    postgresql-client \
    && rm -rf /var/lib/apt/lists/*

# Set environment variables
ENV PATH="/usr/local/go/bin:/root/.cargo/bin:/root/.dotnet:/opt/emsdk:/opt/emsdk/upstream/emscripten:/root/.local/bin:/usr/local/bin:/opt/zokrates/bin:${PATH}"
ENV SOLANA_CONFIG_DIR="/app/.solana"
ENV DOTNET_ROOT="/root/.dotnet"
ENV GOPATH="/app/go"
ENV GOROOT="/usr/local/go"
ENV RUSTUP_HOME="/root/.rustup"
ENV CARGO_HOME="/root/.cargo"
ENV JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64"
ENV LD_LIBRARY_PATH="/usr/local/lib:/opt/SEAL/lib:/opt/HElib/lib:/opt/tfhe/lib:${LD_LIBRARY_PATH}"
ENV IPFS_PATH="/app/.ipfs"
ENV PGHOST="postgres"
ENV PGUSER="postgres"
ENV PGPASSWORD="postgres"
ENV PGDATABASE="postgres"
ENV MONGODB_URI="mongodb://admin:adminpassword@mongodb:27017"
ENV NEO4J_URI="neo4j://neo4j:7687"
ENV NEO4J_USERNAME="neo4j"
ENV NEO4J_PASSWORD="password"
ENV MILVUS_HOST="milvus"
ENV MILVUS_PORT="19530"

# Working directory
WORKDIR /app

# Create project directories
RUN mkdir -p /app/src /app/config /app/go/src /app/go/bin /app/go/pkg /app/.ipfs

# Initialize IPFS configuration
RUN ipfs init

# Create database connection scripts
RUN echo '#!/bin/bash\npsql -h postgres -U postgres -d postgres' > /usr/local/bin/connect-postgres && chmod +x /usr/local/bin/connect-postgres
RUN echo '#!/bin/bash\nmongo mongodb://admin:adminpassword@mongodb:27017' > /usr/local/bin/connect-mongodb && chmod +x /usr/local/bin/connect-mongodb
RUN echo '#!/bin/bash\ncypher-shell -a neo4j:7687 -u neo4j -p password' > /usr/local/bin/connect-neo4j && chmod +x /usr/local/bin/connect-neo4j
RUN echo '#!/bin/bash\npython3 -c "from pymilvus import connections; connections.connect(host=\"milvus\", port=\"19530\"); print(\"Connected to Milvus\")"' > /usr/local/bin/connect-milvus && chmod +x /usr/local/bin/connect-milvus

# Default command (start a shell or code-server)
CMD ["bash"]