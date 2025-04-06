# Frontend Development

This section covers frontend development for web3 applications.

## Table of Contents

- [Introduction](./USAGE-INTRO.md)
- [Blockchain Development](./USAGE-BLOCKCHAIN.md)
- [Layer 2 Development](./USAGE-LAYER2.md)
- [Cross-Chain Development](./USAGE-CROSSCHAIN.md)
- [Smart Wallet Integration](./USAGE-WALLETS.md)
- [Frontend Development](#frontend-development) (this file)
- [Decentralized Storage](./USAGE-STORAGE.md)
- [Database Systems](./USAGE-DATABASES.md)
- [Advanced Cryptography](./USAGE-CRYPTO.md)
- [WebXR/AR/VR Development](./USAGE-WEBXR.md)
- [CI/CD Integration](./USAGE-CICD.md)

## Next.js with Web3

Next.js is a popular React framework that works well for web3 applications.

```bash
# Create a new Next.js app
npx create-next-app my-dapp
cd my-dapp

# Install web3 libraries
npm install ethers web3 @web3-react/core @web3-react/injected-connector

# Start development server
npm run dev
```

Example Next.js page with Web3 integration:

```jsx
// pages/index.js
import { useState, useEffect } from 'react';
import { ethers } from 'ethers';
import Head from 'next/head';

export default function Home() {
  const [account, setAccount] = useState(null);
  const [balance, setBalance] = useState(null);
  const [loading, setLoading] = useState(false);

  const connectWallet = async () => {
    if (typeof window.ethereum !== 'undefined') {
      try {
        setLoading(true);
        // Request account access
        const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
        const provider = new ethers.providers.Web3Provider(window.ethereum);
        const signer = provider.getSigner();
        const address = await signer.getAddress();
        const balance = await provider.getBalance(address);
        
        setAccount(address);
        setBalance(ethers.utils.formatEther(balance));
        setLoading(false);
      } catch (error) {
        console.error('Error connecting to wallet:', error);
        setLoading(false);
      }
    } else {
      alert('Please install MetaMask!');
    }
  };

  return (
    <div className="container">
      <Head>
        <title>Web3 Dapp</title>
        <meta name="description" content="Web3 Dapp Example" />
        <link rel="icon" href="/favicon.ico" />
      </Head>

      <main className="main">
        <h1 className="title">
          Welcome to <a href="#">Web3 Dapp</a>
        </h1>

        <div className="description">
          {!account ? (
            <button onClick={connectWallet} disabled={loading}>
              {loading ? 'Connecting...' : 'Connect Wallet'}
            </button>
          ) : (
            <div>
              <p>Connected Account: {account}</p>
              <p>Balance: {balance} ETH</p>
            </div>
          )}
        </div>
      </main>
    </div>
  );
}
```

## React with Web3

React is a popular JavaScript library for building user interfaces.

```bash
# Create a new React app
npx create-react-app my-web3-app
cd my-web3-app

# Install web3 libraries
npm install ethers web3 @web3-react/core @web3-react/injected-connector

# Start development server
npm start
```

Example React component with Web3 integration:

```jsx
// src/App.js
import React, { useState } from 'react';
import { ethers } from 'ethers';
import './App.css';

function App() {
  const [account, setAccount] = useState('');
  const [balance, setBalance] = useState('');
  const [isConnected, setIsConnected] = useState(false);

  const connectWallet = async () => {
    if (window.ethereum) {
      try {
        const accounts = await window.ethereum.request({
          method: 'eth_requestAccounts',
        });
        const provider = new ethers.providers.Web3Provider(window.ethereum);
        const balance = await provider.getBalance(accounts[0]);
        
        setAccount(accounts[0]);
        setBalance(ethers.utils.formatEther(balance));
        setIsConnected(true);
      } catch (error) {
        console.error(error);
      }
    } else {
      alert('Please install MetaMask!');
    }
  };

  return (
    <div className="App">
      <header className="App-header">
        <h1>React Web3 App</h1>
        {!isConnected ? (
          <button onClick={connectWallet}>Connect Wallet</button>
        ) : (
          <div>
            <p>Connected Account: {account}</p>
            <p>Balance: {balance} ETH</p>
          </div>
        )}
      </header>
    </div>
  );
}

export default App;
```

## Vue.js with Web3

Vue.js is a progressive JavaScript framework for building user interfaces.

```bash
# Create a new Vue.js app
npm init vue@latest my-vue-web3-app
cd my-vue-web3-app

# Install dependencies
npm install

# Install web3 libraries
npm install ethers web3

# Start development server
npm run dev
```

Example Vue component with Web3 integration:

```vue
<!-- src/components/WalletConnect.vue -->
<template>
  <div class="wallet-connect">
    <button v-if="!isConnected" @click="connectWallet" :disabled="isLoading">
      {{ isLoading ? 'Connecting...' : 'Connect Wallet' }}
    </button>
    <div v-else class="wallet-info">
      <p>Connected Account: {{ account }}</p>
      <p>Balance: {{ balance }} ETH</p>
    </div>
  </div>
</template>

<script>
import { ethers } from 'ethers';

export default {
  name: 'WalletConnect',
  data() {
    return {
      account: '',
      balance: '',
      isConnected: false,
      isLoading: false
    };
  },
  methods: {
    async connectWallet() {
      if (window.ethereum) {
        try {
          this.isLoading = true;
          const accounts = await window.ethereum.request({
            method: 'eth_requestAccounts',
          });
          const provider = new ethers.providers.Web3Provider(window.ethereum);
          const balance = await provider.getBalance(accounts[0]);
          
          this.account = accounts[0];
          this.balance = ethers.utils.formatEther(balance);
          this.isConnected = true;
          this.isLoading = false;
        } catch (error) {
          console.error(error);
          this.isLoading = false;
        }
      } else {
        alert('Please install MetaMask!');
      }
    }
  }
};
</script>

<style scoped>
.wallet-connect {
  margin: 20px 0;
}
.wallet-info {
  margin-top: 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}
</style>
```

## React Three Fiber (3D in React)

React Three Fiber is a React renderer for Three.js, making it easy to create 3D graphics.

```bash
# Create a new React app
npx create-react-app my-3d-app
cd my-3d-app

# Install Three.js libraries
npm install three @react-three/fiber @react-three/drei

# Start development server
npm start
```

Example React Three Fiber component:

```jsx
// src/App.js
import React, { useRef } from 'react';
import { Canvas, useFrame } from '@react-three/fiber';
import { OrbitControls } from '@react-three/drei';
import './App.css';

function Box(props) {
  const meshRef = useRef();
  
  useFrame((state, delta) => {
    meshRef.current.rotation.x += delta;
    meshRef.current.rotation.y += delta * 0.5;
  });
  
  return (
    <mesh
      {...props}
      ref={meshRef}
      scale={1.5}
    >
      <boxGeometry args={[1, 1, 1]} />
      <meshStandardMaterial color={props.color || 'orange'} />
    </mesh>
  );
}

function App() {
  return (
    <div className="App">
      <Canvas>
        <ambientLight intensity={0.5} />
        <spotLight position={[10, 10, 10]} angle={0.15} penumbra={1} />
        <Box position={[-1.2, 0, 0]} color="hotpink" />
        <Box position={[1.2, 0, 0]} color="lightblue" />
        <OrbitControls />
      </Canvas>
    </div>
  );
}

export default App;
```

## Tailwind CSS

Tailwind CSS is a utility-first CSS framework that can be used with any frontend framework.

```bash
# Install Tailwind CSS in a Next.js project
cd my-dapp
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Configure Tailwind CSS
# Edit tailwind.config.js:
module.exports = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}

# Add Tailwind directives to CSS
# Edit styles/globals.css:
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Example component using Tailwind CSS:

```jsx
// components/NFTCard.js
export default function NFTCard({ nft }) {
  return (
    <div className="bg-white rounded-lg shadow-md overflow-hidden">
      <img 
        src={nft.image} 
        alt={nft.name} 
        className="w-full h-48 object-cover"
      />
      <div className="p-4">
        <h3 className="text-lg font-semibold text-gray-800">{nft.name}</h3>
        <p className="text-sm text-gray-500 mt-1">{nft.description}</p>
        <div className="mt-4 flex justify-between items-center">
          <span className="text-sm font-medium text-indigo-600">
            {nft.price} ETH
          </span>
          <button className="px-4 py-2 bg-indigo-600 text-white text-sm font-medium rounded-md hover:bg-indigo-700">
            Buy Now
          </button>
        </div>
      </div>
    </div>
  );
}
```

## Ethers.js

Ethers.js is a complete Ethereum library and wallet implementation.

```bash
# Install ethers.js
npm install ethers
```

Example usage:

```javascript
// Connect to Ethereum
const { ethers } = require('ethers');

// Connect to the Ethereum network
const provider = new ethers.providers.JsonRpcProvider('http://geth:8545');

// Create a wallet instance from a private key
const wallet = new ethers.Wallet('0x0123456789012345678901234567890123456789012345678901234567890123', provider);

// Get the wallet address
console.log('Wallet address:', wallet.address);

// Get the balance
const getBalance = async () => {
  const balance = await wallet.getBalance();
  console.log('Balance:', ethers.utils.formatEther(balance), 'ETH');
};

// Send a transaction
const sendTransaction = async () => {
  const tx = await wallet.sendTransaction({
    to: '0xRecipientAddress',
    value: ethers.utils.parseEther('0.1')
  });
  
  console.log('Transaction hash:', tx.hash);
  
  // Wait for the transaction to be mined
  const receipt = await tx.wait();
  console.log('Transaction confirmed in block:', receipt.blockNumber);
};

// Interact with a smart contract
const interactWithContract = async () => {
  const contractAddress = '0xContractAddress';
  const abi = [
    'function balanceOf(address owner) view returns (uint256)',
    'function transfer(address to, uint256 amount) returns (bool)'
  ];
  
  const contract = new ethers.Contract(contractAddress, abi, wallet);
  
  // Call a read-only method
  const balance = await contract.balanceOf(wallet.address);
  console.log('Token balance:', ethers.utils.formatUnits(balance, 18));
  
  // Call a state-changing method
  const transferTx = await contract.transfer('0xRecipientAddress', ethers.utils.parseUnits('10', 18));
  await transferTx.wait();
  console.log('Transfer complete!');
};
```

## Web3.js

Web3.js is a collection of libraries that allow you to interact with a local or remote Ethereum node.

```bash
# Install web3.js
npm install web3
```

Example usage:

```javascript
// Connect to Ethereum
const Web3 = require('web3');
const web3 = new Web3('http://geth:8545');

// Create an account
const account = web3.eth.accounts.create();
console.log('New account address:', account.address);
console.log('Private key:', account.privateKey);

// Import an account from private key
const importedAccount = web3.eth.accounts.privateKeyToAccount('0x0123456789012345678901234567890123456789012345678901234567890123');
web3.eth.accounts.wallet.add(importedAccount);
web3.eth.defaultAccount = importedAccount.address;

// Get the balance
const getBalance = async () => {
  const balance = await web3.eth.getBalance(importedAccount.address);
  console.log('Balance:', web3.utils.fromWei(balance, 'ether'), 'ETH');
};

// Send a transaction
const sendTransaction = async () => {
  const tx = await web3.eth.sendTransaction({
    from: importedAccount.address,
    to: '0xRecipientAddress',
    value: web3.utils.toWei('0.1', 'ether'),
    gas: 21000
  });
  
  console.log('Transaction hash:', tx.transactionHash);
};

// Interact with a smart contract
const interactWithContract = async () => {
  const contractAddress = '0xContractAddress';
  const abi = [
    {
      "constant": true,
      "inputs": [{"name": "owner", "type": "address"}],
      "name": "balanceOf",
      "outputs": [{"name": "", "type": "uint256"}],
      "type": "function"
    },
    {
      "constant": false,
      "inputs": [{"name": "to", "type": "address"}, {"name": "amount", "type": "uint256"}],
      "name": "transfer",
      "outputs": [{"name": "", "type": "bool"}],
      "type": "function"
    }
  ];
  
  const contract = new web3.eth.Contract(abi, contractAddress);
  
  // Call a read-only method
  const balance = await contract.methods.balanceOf(importedAccount.address).call();
  console.log('Token balance:', web3.utils.fromWei(balance, 'ether'));
  
  // Call a state-changing method
  const transferTx = await contract.methods.transfer(
    '0xRecipientAddress',
    web3.utils.toWei('10', 'ether')
  ).send({
    from: importedAccount.address,
    gas: 100000
  });
  
  console.log('Transfer complete! Transaction hash:', transferTx.transactionHash);
};
```

## Wagmi

Wagmi is a collection of React Hooks for Ethereum.

```bash
# Install wagmi
npm install wagmi ethers@5.7.2

# Create a new React app with wagmi
npx create-react-app wagmi-app
cd wagmi-app
npm install wagmi ethers@5.7.2
```

Example usage:

```jsx
// src/App.js
import { WagmiConfig, createClient, configureChains, mainnet } from 'wagmi';
import { publicProvider } from 'wagmi/providers/public';
import { MetaMaskConnector } from 'wagmi/connectors/metaMask';
import Profile from './Profile';

const { chains, provider, webSocketProvider } = configureChains(
  [mainnet],
  [publicProvider()],
);

const client = createClient({
  autoConnect: true,
  connectors: [
    new MetaMaskConnector({ chains }),
  ],
  provider,
  webSocketProvider,
});

function App() {
  return (
    <WagmiConfig client={client}>
      <div className="App">
        <h1>Wagmi Example</h1>
        <Profile />
      </div>
    </WagmiConfig>
  );
}

export default App;

// src/Profile.js
import { useAccount, useConnect, useDisconnect, useBalance } from 'wagmi';
import { MetaMaskConnector } from 'wagmi/connectors/metaMask';

function Profile() {
  const { address, isConnected } = useAccount();
  const { connect } = useConnect({
    connector: new MetaMaskConnector(),
  });
  const { disconnect } = useDisconnect();
  const { data: balance } = useBalance({
    address: address,
  });

  if (isConnected) {
    return (
      <div>
        <div>Connected to {address}</div>
        <div>Balance: {balance?.formatted} {balance?.symbol}</div>
        <button onClick={() => disconnect()}>Disconnect</button>
      </div>
    );
  }
  
  return <button onClick={() => connect()}>Connect Wallet</button>;
}

export default Profile;