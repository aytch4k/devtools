# Smart Wallet Integration

This section covers integration with various wallet technologies and account abstraction.

## Table of Contents

- [Introduction](./USAGE-INTRO.md)
- [Blockchain Development](./USAGE-BLOCKCHAIN.md)
- [Layer 2 Development](./USAGE-LAYER2.md)
- [Cross-Chain Development](./USAGE-CROSSCHAIN.md)
- [Smart Wallet Integration](#smart-wallet-integration) (this file)
- [Frontend Development](./USAGE-FRONTEND.md)
- [Decentralized Storage](./USAGE-STORAGE.md)
- [Database Systems](./USAGE-DATABASES.md)
- [Advanced Cryptography](./USAGE-CRYPTO.md)
- [WebXR/AR/VR Development](./USAGE-WEBXR.md)
- [CI/CD Integration](./USAGE-CICD.md)

## MetaMask SDK

MetaMask is the most widely used Ethereum wallet browser extension.

```bash
# Create a new React app with MetaMask integration
npx create-react-app metamask-app
cd metamask-app
npm install @metamask/sdk ethers

# Create a basic MetaMask integration
cat > src/App.js << EOF
import React, { useState, useEffect } from 'react';
import { MetaMaskSDK } from '@metamask/sdk';
import { ethers } from 'ethers';

function App() {
  const [account, setAccount] = useState(null);
  const [sdk, setSDK] = useState(null);

  useEffect(() => {
    const initSDK = async () => {
      const MMSDK = new MetaMaskSDK({
        dappMetadata: {
          name: "My Dapp",
          url: window.location.href,
        }
      });
      setSDK(MMSDK);
    };
    initSDK();
  }, []);

  const connectWallet = async () => {
    try {
      const accounts = await sdk.connect();
      setAccount(accounts[0]);
    } catch (error) {
      console.error('Error connecting to MetaMask', error);
    }
  };

  const sendTransaction = async () => {
    if (!account) return;
    
    try {
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const signer = provider.getSigner();
      
      const tx = await signer.sendTransaction({
        to: "0xRecipientAddress",
        value: ethers.utils.parseEther("0.001")
      });
      
      console.log('Transaction sent:', tx.hash);
    } catch (error) {
      console.error('Error sending transaction', error);
    }
  };

  return (
    <div className="App">
      <header className="App-header">
        <h1>MetaMask SDK Demo</h1>
        {!account ? (
          <button onClick={connectWallet}>Connect Wallet</button>
        ) : (
          <>
            <p>Connected: {account}</p>
            <button onClick={sendTransaction}>Send Transaction</button>
          </>
        )}
      </header>
    </div>
  );
}

export default App;
EOF

# Start the app
npm start
```

## Account Abstraction (EIP-4337)

Account Abstraction allows for smart contract wallets with advanced features.

```bash
# Create a new project for Account Abstraction
mkdir aa-project && cd aa-project
npm init -y
npm install @account-abstraction/sdk ethers

# Create a simple account abstraction script
cat > index.js << EOF
const { SimpleAccountAPI, HttpRpcClient } = require('@account-abstraction/sdk');
const { ethers } = require('ethers');

async function main() {
  // Connect to the Ethereum node
  const provider = new ethers.providers.JsonRpcProvider('http://geth:8545');
  const signer = new ethers.Wallet('your-private-key', provider);
  
  // Initialize the SimpleAccountAPI
  const accountAPI = new SimpleAccountAPI({
    provider,
    entryPointAddress: '0xEntryPointAddress',
    owner: signer,
    factoryAddress: '0xFactoryAddress'
  });
  
  // Get the counterfactual address of the smart account
  const address = await accountAPI.getCounterFactualAddress();
  console.log('Smart Account Address:', address);
  
  // Fund the account with ETH
  await signer.sendTransaction({
    to: address,
    value: ethers.utils.parseEther('0.1')
  });
  
  // Create a user operation to send ETH
  const op = await accountAPI.createSignedUserOp({
    target: '0xRecipientAddress',
    value: ethers.utils.parseEther('0.01'),
    data: '0x'
  });
  
  // Send the user operation via the bundler
  const client = new HttpRpcClient('http://bundler:3000/rpc');
  const uoHash = await client.sendUserOpToBundler(op);
  console.log('UserOperation hash:', uoHash);
}

main().catch(console.error);
EOF

# Run the script
node index.js
```

Example Account Abstraction contract:

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity ^0.8.12;

import "@account-abstraction/contracts/core/BaseAccount.sol";
import "@account-abstraction/contracts/core/Helpers.sol";
import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

contract SimpleAccount is BaseAccount {
    using ECDSA for bytes32;

    address public owner;
    IEntryPoint private immutable _entryPoint;

    event SimpleAccountInitialized(IEntryPoint indexed entryPoint, address indexed owner);

    modifier onlyOwner() {
        require(msg.sender == owner, "only owner");
        _;
    }

    constructor(IEntryPoint anEntryPoint) {
        _entryPoint = anEntryPoint;
    }

    function initialize(address anOwner) public {
        require(owner == address(0), "already initialized");
        owner = anOwner;
        emit SimpleAccountInitialized(_entryPoint, owner);
    }

    function _validateSignature(UserOperation calldata userOp, bytes32 userOpHash)
    internal override virtual returns (uint256 validationData) {
        bytes32 hash = userOpHash.toEthSignedMessageHash();
        if (owner != hash.recover(userOp.signature))
            return SIG_VALIDATION_FAILED;
        return 0;
    }

    function entryPoint() public view override returns (IEntryPoint) {
        return _entryPoint;
    }

    function execute(address dest, uint256 value, bytes calldata func) external onlyOwner {
        _call(dest, value, func);
    }

    function executeBatch(address[] calldata dest, uint256[] calldata value, bytes[] calldata func) external onlyOwner {
        require(dest.length == func.length && (value.length == 0 || value.length == func.length), "wrong array lengths");
        if (value.length == 0) {
            for (uint256 i = 0; i < dest.length; i++) {
                _call(dest[i], 0, func[i]);
            }
        } else {
            for (uint256 i = 0; i < dest.length; i++) {
                _call(dest[i], value[i], func[i]);
            }
        }
    }

    function _call(address target, uint256 value, bytes memory data) internal {
        (bool success, bytes memory result) = target.call{value: value}(data);
        if (!success) {
            assembly {
                revert(add(result, 32), mload(result))
            }
        }
    }
}
```

## Gnosis Safe

Gnosis Safe is a popular multi-signature wallet solution.

```bash
# Create a new project for Gnosis Safe integration
mkdir gnosis-safe-project && cd gnosis-safe-project
npm init -y
npm install @gnosis.pm/safe-core-sdk @gnosis.pm/safe-ethers-lib @gnosis.pm/safe-service-client ethers

# Create a script to create and interact with a Gnosis Safe
cat > index.js << EOF
const { SafeFactory, SafeAccountConfig } = require('@gnosis.pm/safe-core-sdk');
const { EthersAdapter } = require('@gnosis.pm/safe-ethers-lib');
const { SafeServiceClient } = require('@gnosis.pm/safe-service-client');
const { ethers } = require('ethers');

async function main() {
  // Connect to the Ethereum node
  const provider = new ethers.providers.JsonRpcProvider('http://geth:8545');
  const signer = new ethers.Wallet('your-private-key', provider);
  
  // Initialize EthAdapter
  const ethAdapter = new EthersAdapter({
    ethers,
    signer
  });
  
  // Create a Safe Factory instance
  const safeFactory = await SafeFactory.create({ ethAdapter });
  
  // Define owners and threshold
  const owners = ['0xOwner1', '0xOwner2', '0xOwner3'];
  const threshold = 2; // Require 2 out of 3 owners to sign transactions
  
  // Create a Safe Account Config
  const safeAccountConfig = {
    owners,
    threshold
  };
  
  // Deploy the Safe
  const safeSdk = await safeFactory.deploySafe({ safeAccountConfig });
  const safeAddress = safeSdk.getAddress();
  console.log('Safe deployed at:', safeAddress);
  
  // Create a transaction
  const transaction = {
    to: '0xRecipientAddress',
    value: ethers.utils.parseEther('0.01').toString(),
    data: '0x'
  };
  
  // Create a Safe transaction
  const safeTransaction = await safeSdk.createTransaction(transaction);
  
  // Sign the transaction with the first owner
  await safeSdk.signTransaction(safeTransaction);
  
  // Get the transaction hash
  const txHash = await safeSdk.getTransactionHash(safeTransaction);
  console.log('Transaction hash:', txHash);
  
  // Execute the transaction (would require more signatures in a real scenario)
  // const executeTxResponse = await safeSdk.executeTransaction(safeTransaction);
  // console.log('Transaction executed:', executeTxResponse.transactionHash);
}

main().catch(console.error);
EOF

# Run the script
node index.js
```

## WalletConnect

WalletConnect is an open protocol for connecting wallets to dApps.

```bash
# Create a new project with WalletConnect integration
mkdir walletconnect-project && cd walletconnect-project
npm init -y
npm install @walletconnect/web3-provider ethers

# Create a basic WalletConnect integration
cat > index.js << EOF
const { ethers } = require('ethers');
const WalletConnectProvider = require('@walletconnect/web3-provider').default;

async function main() {
  // Create WalletConnect Provider
  const provider = new WalletConnectProvider({
    infuraId: "your-infura-id", // Replace with your Infura ID
    rpc: {
      1: "http://geth:8545", // Ethereum Mainnet
    },
    qrcode: true,
  });
  
  // Enable session (triggers QR Code modal)
  await provider.enable();
  
  // Create Web3Provider using WalletConnect
  const web3Provider = new ethers.providers.Web3Provider(provider);
  const signer = web3Provider.getSigner();
  const address = await signer.getAddress();
  console.log('Connected account:', address);
  
  // Send a transaction
  const tx = await signer.sendTransaction({
    to: "0xRecipientAddress",
    value: ethers.utils.parseEther("0.01")
  });
  console.log('Transaction sent:', tx.hash);
  
  // Close provider session
  await provider.disconnect();
}

main().catch(console.error);
EOF

# Run the script
node index.js
```

## Trust Wallet Core

Trust Wallet Core is a cross-platform library that implements low-level cryptographic wallet functionality.

```bash
# Create a new project with Trust Wallet Core
mkdir trust-wallet-project && cd trust-wallet-project
npm init -y

# For JavaScript/TypeScript projects, you can use the wallet-core-js package
npm install @trustwallet/wallet-core

# Create a basic script to generate a wallet
cat > index.js << EOF
const WalletCore = require('@trustwallet/wallet-core');

async function main() {
  // Wait for wallet core to be loaded
  await WalletCore.loadWasm();
  
  // Generate a mnemonic
  const mnemonic = WalletCore.Mnemonic.generateMnemonic();
  console.log('Mnemonic:', mnemonic);
  
  // Create a wallet from the mnemonic
  const wallet = WalletCore.HDWallet.createWithMnemonic(mnemonic);
  
  // Get Ethereum address
  const ethAddress = wallet.getAddressForCoin(WalletCore.CoinType.ethereum);
  console.log('Ethereum address:', ethAddress);
  
  // Get Bitcoin address
  const btcAddress = wallet.getAddressForCoin(WalletCore.CoinType.bitcoin);
  console.log('Bitcoin address:', btcAddress);
  
  // Get Solana address
  const solAddress = wallet.getAddressForCoin(WalletCore.CoinType.solana);
  console.log('Solana address:', solAddress);
}

main().catch(console.error);
EOF

# Run the script
node index.js
```

## Biconomy Smart Accounts

Biconomy provides account abstraction infrastructure.

```bash
# Create a new project with Biconomy Smart Accounts
mkdir biconomy-project && cd biconomy-project
npm init -y
npm install @biconomy/smart-account @biconomy/core-types ethers

# Create a script to create and use a Biconomy Smart Account
cat > index.js << EOF
const { BiconomySmartAccountV2, DEFAULT_ENTRYPOINT_ADDRESS } = require("@biconomy/account");
const { Bundler } = require("@biconomy/bundler");
const { ChainId } = require("@biconomy/core-types");
const { ethers } = require("ethers");

async function main() {
  // Initialize provider
  const provider = new ethers.providers.JsonRpcProvider("http://geth:8545");
  const signer = new ethers.Wallet("your-private-key", provider);
  
  // Create bundler instance
  const bundler = new Bundler({
    bundlerUrl: "https://bundler.biconomy.io/api/v2/80001/nJPK7B3ru.dd7f7861-190d-41bd-af80-6877f74b8f44",
    chainId: ChainId.POLYGON_MUMBAI,
    entryPointAddress: DEFAULT_ENTRYPOINT_ADDRESS,
  });
  
  // Create smart account instance
  const smartAccount = await BiconomySmartAccountV2.create({
    chainId: ChainId.POLYGON_MUMBAI,
    bundler: bundler,
    signer: signer,
  });
  
  // Get smart account address
  const address = await smartAccount.getAccountAddress();
  console.log("Smart Account Address:", address);
  
  // Create a transaction
  const transaction = {
    to: "0xRecipientAddress",
    data: "0x",
    value: ethers.utils.parseEther("0.01"),
  };
  
  // Send the transaction
  const userOp = await smartAccount.buildUserOp([transaction]);
  const userOpResponse = await smartAccount.sendUserOp(userOp);
  
  // Get transaction hash
  const transactionHash = await userOpResponse.wait();
  console.log("Transaction Hash:", transactionHash);
}

main().catch(console.error);
EOF

# Run the script
node index.js
```

## Coinbase Wallet SDK

Coinbase Wallet SDK allows dApps to connect to the Coinbase Wallet.

```bash
# Create a new project with Coinbase Wallet SDK
mkdir coinbase-wallet-project && cd coinbase-wallet-project
npm init -y
npm install @coinbase/wallet-sdk ethers

# Create a basic integration
cat > index.js << EOF
const CoinbaseWalletSDK = require('@coinbase/wallet-sdk');
const { ethers } = require('ethers');

async function main() {
  // Initialize Coinbase Wallet SDK
  const coinbaseWallet = new CoinbaseWalletSDK({
    appName: 'My dApp',
    appLogoUrl: 'https://example.com/logo.png',
    darkMode: false
  });
  
  // Create Web3 Provider
  const ethereum = coinbaseWallet.makeWeb3Provider('http://geth:8545', 1);
  
  // Request accounts
  const accounts = await ethereum.request({ method: 'eth_requestAccounts' });
  console.log('Connected accounts:', accounts);
  
  // Create ethers provider
  const provider = new ethers.providers.Web3Provider(ethereum);
  const signer = provider.getSigner();
  
  // Send a transaction
  const tx = await signer.sendTransaction({
    to: '0xRecipientAddress',
    value: ethers.utils.parseEther('0.01')
  });
  console.log('Transaction hash:', tx.hash);
}

main().catch(console.error);
EOF

# Run the script
node index.js
```

## Ledger Hardware Wallet Integration

Ledger is a popular hardware wallet for secure key storage.

```bash
# Create a new project with Ledger integration
mkdir ledger-project && cd ledger-project
npm init -y
npm install @ledgerhq/hw-app-eth @ledgerhq/hw-transport-node-hid ethers

# Create a script to interact with a Ledger device
cat > index.js << EOF
const { default: TransportNodeHid } = require('@ledgerhq/hw-transport-node-hid');
const Eth = require('@ledgerhq/hw-app-eth').default;
const { ethers } = require('ethers');

async function main() {
  // Open connection to Ledger device
  const transport = await TransportNodeHid.open();
  const eth = new Eth(transport);
  
  // Get Ethereum address from Ledger (path for first account)
  const path = "44'/60'/0'/0/0";
  const result = await eth.getAddress(path);
  console.log('Ethereum address:', result.address);
  
  // Sign a transaction using Ledger
  const provider = new ethers.providers.JsonRpcProvider('http://geth:8545');
  
  // Create transaction
  const unsignedTx = {
    to: '0xRecipientAddress',
    value: ethers.utils.parseEther('0.01'),
    gasLimit: ethers.utils.hexlify(21000),
    gasPrice: ethers.utils.hexlify(await provider.getGasPrice()),
    nonce: ethers.utils.hexlify(await provider.getTransactionCount(result.address)),
    chainId: 1
  };
  
  // Serialize transaction
  const serializedTx = ethers.utils.serializeTransaction(unsignedTx).substring(2);
  
  // Sign transaction with Ledger
  const signature = await eth.signTransaction(path, serializedTx);
  
  // Create signed transaction
  const signedTx = ethers.utils.serializeTransaction(unsignedTx, {
    v: parseInt(signature.v, 16),
    r: '0x' + signature.r,
    s: '0x' + signature.s
  });
  
  // Broadcast transaction
  const tx = await provider.sendTransaction(signedTx);
  console.log('Transaction hash:', tx.hash);
  
  // Close connection
  await transport.close();
}

main().catch(console.error);
EOF

# Run the script
node index.js