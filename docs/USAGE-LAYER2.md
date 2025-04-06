# Layer 2 Development

This section covers development on various Layer 2 scaling solutions.

## Table of Contents

- [Introduction](./USAGE-INTRO.md)
- [Blockchain Development](./USAGE-BLOCKCHAIN.md)
- [Layer 2 Development](#layer-2-development) (this file)
- [Cross-Chain Development](./USAGE-CROSSCHAIN.md)
- [Smart Wallet Integration](./USAGE-WALLETS.md)
- [Frontend Development](./USAGE-FRONTEND.md)
- [Decentralized Storage](./USAGE-STORAGE.md)
- [Database Systems](./USAGE-DATABASES.md)
- [Advanced Cryptography](./USAGE-CRYPTO.md)
- [WebXR/AR/VR Development](./USAGE-WEBXR.md)
- [CI/CD Integration](./USAGE-CICD.md)

## zkSync

zkSync is a ZK rollup that scales Ethereum with zero-knowledge proofs.

```bash
# Initialize a zkSync project
mkdir zksync-project && cd zksync-project
npx hardhat init

# Install zkSync plugins
npm install -D @matterlabs/hardhat-zksync-solc @matterlabs/hardhat-zksync-deploy

# Configure Hardhat for zkSync
# Edit hardhat.config.js:
require("@matterlabs/hardhat-zksync-deploy");
require("@matterlabs/hardhat-zksync-solc");

module.exports = {
  zksolc: {
    version: "1.3.5",
    compilerSource: "binary",
  },
  networks: {
    zkSyncLocal: {
      url: "http://zksync-node:3030",
      ethNetwork: "http://geth:8545",
      zksync: true,
    },
  },
  solidity: {
    version: "0.8.17",
  },
};

# Compile and deploy
npx hardhat compile
npx hardhat deploy-zksync
```

Example zkSync contract:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Counter {
    uint256 private count = 0;
    
    event CountIncremented(uint256 newCount);
    
    function increment() public {
        count += 1;
        emit CountIncremented(count);
    }
    
    function getCount() public view returns (uint256) {
        return count;
    }
}
```

Example deployment script:

```javascript
// deploy.js
const { Wallet } = require("zksync-web3");
const { Deployer } = require("@matterlabs/hardhat-zksync-deploy");
const hre = require("hardhat");

async function main() {
  // Initialize the wallet
  const wallet = new Wallet("<PRIVATE_KEY>");

  // Create deployer
  const deployer = new Deployer(hre, wallet);
  
  // Deploy the contract
  const artifact = await deployer.loadArtifact("Counter");
  const counter = await deployer.deploy(artifact);
  
  console.log(`Counter deployed to: ${counter.address}`);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

## StarkNet

StarkNet is a permissionless decentralized ZK-Rollup operating as an L2 network over Ethereum.

```bash
# Initialize a StarkNet project
mkdir starknet-project && cd starknet-project

# Create a Cairo contract
cat > contract.cairo << EOF
%lang starknet

@storage_var
func balance() -> (res: felt) {
}

@external
func increase_balance{syscall_ptr: felt*, pedersen_ptr: HashBuiltin*, range_check_ptr}(amount: felt) {
    let (current_balance) = balance.read();
    balance.write(current_balance + amount);
    return ();
}

@view
func get_balance{syscall_ptr: felt*, pedersen_ptr: HashBuiltin*, range_check_ptr}() -> (res: felt) {
    let (res) = balance.read();
    return (res,);
}
EOF

# Compile the contract
starknet-compile contract.cairo --output contract_compiled.json

# Deploy to local StarkNet
starknet deploy --contract contract_compiled.json --network http://starknet-node:5050

# Interact with the contract
starknet invoke --address <contract_address> --abi contract_compiled_abi.json --function increase_balance --inputs 10
starknet call --address <contract_address> --abi contract_compiled_abi.json --function get_balance
```

Example Cairo 1.0 contract:

```cairo
#[contract]
mod Counter {
    use starknet::get_caller_address;
    use starknet::ContractAddress;

    #[event]
    fn CounterIncremented(counter: u256, account: ContractAddress) {}

    #[storage]
    struct Storage {
        counter: u256,
    }

    #[external]
    fn increment() {
        let caller = get_caller_address();
        let current = counter::read();
        counter::write(current + 1);
        CounterIncremented(current + 1, caller);
    }

    #[view]
    fn get_counter() -> u256 {
        counter::read()
    }
}
```

## Optimism

Optimism is an EVM-equivalent optimistic rollup.

```bash
# Initialize an Optimism project
mkdir optimism-project && cd optimism-project
npx hardhat init

# Configure Hardhat for Optimism
# Edit hardhat.config.js:
module.exports = {
  networks: {
    optimism: {
      url: "http://optimism-node:8545",
      accounts: [privateKey],
    },
  },
  solidity: "0.8.17",
};

# Compile and deploy
npx hardhat compile
npx hardhat run scripts/deploy.js --network optimism

# Interact with the contract
npx hardhat console --network optimism
> const Contract = await ethers.getContractFactory("YourContract")
> const contract = await Contract.attach("YourContractAddress")
> await contract.yourFunction()
```

Example Optimism contract (same as Ethereum, as Optimism is EVM-equivalent):

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract OptimisticCounter {
    uint256 private count = 0;
    address public owner;
    
    constructor() {
        owner = msg.sender;
    }
    
    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }
    
    function increment() public {
        count += 1;
    }
    
    function adminReset() public onlyOwner {
        count = 0;
    }
    
    function getCount() public view returns (uint256) {
        return count;
    }
}
```

## Arbitrum

Arbitrum is an optimistic rollup scaling solution for Ethereum.

```bash
# Initialize an Arbitrum project
mkdir arbitrum-project && cd arbitrum-project
npx hardhat init

# Configure Hardhat for Arbitrum
# Edit hardhat.config.js:
module.exports = {
  networks: {
    arbitrum: {
      url: "http://arbitrum-node:8547",
      accounts: [privateKey],
    },
  },
  solidity: "0.8.17",
};

# Compile and deploy
npx hardhat compile
npx hardhat run scripts/deploy.js --network arbitrum

# Interact with the contract
npx hardhat console --network arbitrum
> const Contract = await ethers.getContractFactory("YourContract")
> const contract = await Contract.attach("YourContractAddress")
> await contract.yourFunction()
```

Example Arbitrum contract (same as Ethereum, as Arbitrum is EVM-equivalent):

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract ArbitrumStorage {
    mapping(address => string) private userMessages;
    
    event MessageStored(address indexed user, string message);
    
    function storeMessage(string memory message) public {
        userMessages[msg.sender] = message;
        emit MessageStored(msg.sender, message);
    }
    
    function getMessage(address user) public view returns (string memory) {
        return userMessages[user];
    }
}
```

## Polygon zkEVM

Polygon zkEVM is a ZK rollup that's fully compatible with existing Ethereum smart contracts.

```bash
# Initialize a Polygon zkEVM project
mkdir polygon-zkevm-project && cd polygon-zkevm-project
npx hardhat init

# Configure Hardhat for Polygon zkEVM
# Edit hardhat.config.js:
module.exports = {
  networks: {
    polygonZkEVM: {
      url: "http://polygon-zkevm-node:8123",
      accounts: [privateKey],
    },
  },
  solidity: "0.8.17",
};

# Compile and deploy
npx hardhat compile
npx hardhat run scripts/deploy.js --network polygonZkEVM

# Interact with the contract
npx hardhat console --network polygonZkEVM
> const Contract = await ethers.getContractFactory("YourContract")
> const contract = await Contract.attach("YourContractAddress")
> await contract.yourFunction()
```

Example Polygon zkEVM contract (same as Ethereum, as Polygon zkEVM is EVM-equivalent):

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract PolygonZkEVMNFT {
    mapping(uint256 => address) private _owners;
    mapping(address => uint256) private _balances;
    uint256 private _tokenIdCounter;
    
    event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);
    
    constructor() {
        _tokenIdCounter = 0;
    }
    
    function mint(address to) public {
        uint256 tokenId = _tokenIdCounter;
        _tokenIdCounter++;
        
        _owners[tokenId] = to;
        _balances[to]++;
        
        emit Transfer(address(0), to, tokenId);
    }
    
    function ownerOf(uint256 tokenId) public view returns (address) {
        address owner = _owners[tokenId];
        require(owner != address(0), "Token doesn't exist");
        return owner;
    }
    
    function balanceOf(address owner) public view returns (uint256) {
        return _balances[owner];
    }
}
```

## Scroll

Scroll is a zkEVM-based zkRollup on Ethereum.

```bash
# Initialize a Scroll project
mkdir scroll-project && cd scroll-project
npx hardhat init

# Configure Hardhat for Scroll
# Edit hardhat.config.js:
module.exports = {
  networks: {
    scroll: {
      url: "http://scroll-node:8545",
      accounts: [privateKey],
    },
  },
  solidity: "0.8.17",
};

# Compile and deploy
npx hardhat compile
npx hardhat run scripts/deploy.js --network scroll
```

## Loopring

Loopring is a zkRollup protocol for scalable trading and payment on Ethereum.

```bash
# Initialize a Loopring project
mkdir loopring-project && cd loopring-project
npm init -y
npm install @loopring-web/loopring-sdk

# Create a script to interact with Loopring
cat > index.js << EOF
const { LoopringAPI, ChainId } = require('@loopring-web/loopring-sdk');

async function main() {
  // Initialize the SDK
  const api = new LoopringAPI.UserAPI({
    chainId: ChainId.GOERLI,
  });
  
  // Get exchange information
  const exchangeInfo = await api.getExchangeInfo();
  console.log('Exchange Info:', exchangeInfo);
}

main().catch(console.error);
EOF

# Run the script
node index.js