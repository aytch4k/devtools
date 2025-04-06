# Decentralized Storage

This section covers decentralized storage technologies and their integration.

## Table of Contents

- [Introduction](./USAGE-INTRO.md)
- [Blockchain Development](./USAGE-BLOCKCHAIN.md)
- [Layer 2 Development](./USAGE-LAYER2.md)
- [Cross-Chain Development](./USAGE-CROSSCHAIN.md)
- [Smart Wallet Integration](./USAGE-WALLETS.md)
- [Frontend Development](./USAGE-FRONTEND.md)
- [Decentralized Storage](#decentralized-storage) (this file)
- [Database Systems](./USAGE-DATABASES.md)
- [Advanced Cryptography](./USAGE-CRYPTO.md)
- [WebXR/AR/VR Development](./USAGE-WEBXR.md)
- [CI/CD Integration](./USAGE-CICD.md)

## IPFS

InterPlanetary File System (IPFS) is a protocol and peer-to-peer network for storing and sharing data in a distributed file system.

```bash
# Initialize IPFS (already done in the container)
ipfs init

# Start IPFS daemon (if not already running)
ipfs daemon &

# Add files to IPFS
ipfs add -r ./my-website

# Pin important files
ipfs pin add <CID>

# Access via gateway
# http://localhost:8081/ipfs/<CID>
```

Example JavaScript integration with IPFS:

```javascript
// Using ipfs-http-client
const { create } = require('ipfs-http-client');

async function main() {
  // Connect to the local IPFS daemon
  const ipfs = create({ host: 'ipfs', port: 5001, protocol: 'http' });
  
  // Add a file to IPFS
  const file = 'Hello, IPFS!';
  const { cid } = await ipfs.add(file);
  console.log('Added file with CID:', cid.toString());
  
  // Retrieve the file from IPFS
  const chunks = [];
  for await (const chunk of ipfs.cat(cid)) {
    chunks.push(chunk);
  }
  console.log('Retrieved file content:', Buffer.concat(chunks).toString());
  
  // List pins
  console.log('Pins:');
  for await (const pin of ipfs.pin.ls()) {
    console.log(pin.cid.toString());
  }
}

main().catch(console.error);
```

Example React component for uploading to IPFS:

```jsx
import React, { useState } from 'react';
import { create } from 'ipfs-http-client';

// Configure IPFS client
const ipfs = create({ host: 'ipfs', port: 5001, protocol: 'http' });

function IPFSUploader() {
  const [file, setFile] = useState(null);
  const [cid, setCid] = useState('');
  const [loading, setLoading] = useState(false);
  
  const handleFileChange = (e) => {
    setFile(e.target.files[0]);
  };
  
  const handleUpload = async () => {
    if (!file) return;
    
    try {
      setLoading(true);
      
      // Create a buffer from the file
      const buffer = await file.arrayBuffer();
      
      // Add the file to IPFS
      const result = await ipfs.add(buffer);
      setCid(result.cid.toString());
      
      // Pin the file
      await ipfs.pin.add(result.cid);
      
      setLoading(false);
    } catch (error) {
      console.error('Error uploading to IPFS:', error);
      setLoading(false);
    }
  };
  
  return (
    <div>
      <h2>Upload to IPFS</h2>
      <input type="file" onChange={handleFileChange} />
      <button onClick={handleUpload} disabled={!file || loading}>
        {loading ? 'Uploading...' : 'Upload'}
      </button>
      
      {cid && (
        <div>
          <p>File uploaded successfully!</p>
          <p>CID: {cid}</p>
          <p>
            View file: <a href={`http://localhost:8081/ipfs/${cid}`} target="_blank" rel="noopener noreferrer">
              http://localhost:8081/ipfs/{cid}
            </a>
          </p>
        </div>
      )}
    </div>
  );
}

export default IPFSUploader;
```

## IPFS Cluster

IPFS Cluster provides data orchestration across a swarm of IPFS daemons.

```bash
# Initialize IPFS Cluster
ipfs-cluster-service init

# Start IPFS Cluster
ipfs-cluster-service daemon &

# Add and pin content across the cluster
ipfs-cluster-ctl add -r ./my-website

# List pinned content
ipfs-cluster-ctl pin ls

# Remove a pin
ipfs-cluster-ctl pin rm <CID>
```

Example JavaScript integration with IPFS Cluster:

```javascript
const axios = require('axios');

async function main() {
  // Configure IPFS Cluster API client
  const clusterApi = axios.create({
    baseURL: 'http://localhost:9094/api/v0',
    headers: {
      'Content-Type': 'application/json',
    },
  });
  
  // List peers in the cluster
  const peersResponse = await clusterApi.get('/peers');
  console.log('Cluster peers:', peersResponse.data);
  
  // List pinned content
  const pinsResponse = await clusterApi.get('/pins');
  console.log('Pinned content:', pinsResponse.data);
  
  // Add content to the cluster
  const formData = new FormData();
  formData.append('file', new Blob(['Hello, IPFS Cluster!'], { type: 'text/plain' }));
  
  const addResponse = await axios.post('http://localhost:9094/api/v0/add', formData, {
    headers: {
      'Content-Type': 'multipart/form-data',
    },
  });
  
  console.log('Added content:', addResponse.data);
}

main().catch(console.error);
```

## Filecoin

Filecoin is a decentralized storage network built on top of IPFS.

```bash
# Initialize Lotus
lotus daemon

# Create a wallet
lotus wallet new

# Store data on Filecoin
lotus client deal <data-cid> <miner> <price> <duration>
```

Example JavaScript integration with Filecoin:

```javascript
const { LotusRPC } = require('@filecoin-shipyard/lotus-client-rpc');
const { NodejsProvider } = require('@filecoin-shipyard/lotus-client-provider-nodejs');
const { mainnet } = require('@filecoin-shipyard/lotus-client-schema');

async function main() {
  // Connect to Lotus node
  const provider = new NodejsProvider('http://lotus:1234/rpc/v0');
  const client = new LotusRPC(provider, { schema: mainnet.fullNode });
  
  // Get chain head
  const head = await client.chainHead();
  console.log('Chain head:', head);
  
  // Create a new wallet address
  const address = await client.walletNew('bls');
  console.log('New wallet address:', address);
  
  // Get wallet balance
  const balance = await client.walletBalance(address);
  console.log('Wallet balance:', balance);
  
  // List miners
  const miners = await client.stateListMiners(head.Cids);
  console.log('Miners:', miners.slice(0, 10)); // Show first 10 miners
  
  // Get miner info
  const minerInfo = await client.stateMinerInfo(miners[0], head.Cids);
  console.log('Miner info:', minerInfo);
}

main().catch(console.error);
```

## Arweave

Arweave is a decentralized storage network that aims to provide permanent data storage.

```bash
# Generate wallet
arweave-deploy --create-wallet wallet.json

# Deploy to Arweave
arweave-deploy --key-file wallet.json --package index.html
```

Example JavaScript integration with Arweave:

```javascript
const Arweave = require('arweave');

async function main() {
  // Initialize Arweave
  const arweave = Arweave.init({
    host: 'arweave.net',
    port: 443,
    protocol: 'https',
  });
  
  // Load wallet from file
  const wallet = await arweave.wallets.generate();
  
  // Get wallet address
  const address = await arweave.wallets.jwkToAddress(wallet);
  console.log('Wallet address:', address);
  
  // Get wallet balance
  const balance = await arweave.wallets.getBalance(address);
  const ar = arweave.ar.winstonToAr(balance);
  console.log('Wallet balance:', ar, 'AR');
  
  // Create a data transaction
  const data = 'Hello, Arweave!';
  const transaction = await arweave.createTransaction({ data }, wallet);
  
  // Add tags to the transaction
  transaction.addTag('Content-Type', 'text/plain');
  transaction.addTag('App-Name', 'ArweaveExample');
  
  // Sign the transaction
  await arweave.transactions.sign(transaction, wallet);
  
  // Submit the transaction
  const response = await arweave.transactions.post(transaction);
  console.log('Transaction submitted:', response.status);
  
  // Get transaction status
  const status = await arweave.transactions.getStatus(transaction.id);
  console.log('Transaction status:', status);
  
  console.log('Data will be available at:', `https://arweave.net/${transaction.id}`);
}

main().catch(console.error);
```

Example React component for uploading to Arweave:

```jsx
import React, { useState } from 'react';
import Arweave from 'arweave';

// Initialize Arweave
const arweave = Arweave.init({
  host: 'arweave.net',
  port: 443,
  protocol: 'https',
});

function ArweaveUploader() {
  const [file, setFile] = useState(null);
  const [wallet, setWallet] = useState(null);
  const [txId, setTxId] = useState('');
  const [loading, setLoading] = useState(false);
  
  const handleFileChange = (e) => {
    setFile(e.target.files[0]);
  };
  
  const handleWalletUpload = async (e) => {
    const fileReader = new FileReader();
    fileReader.onload = async (e) => {
      try {
        const key = JSON.parse(e.target.result);
        setWallet(key);
      } catch (error) {
        console.error('Error loading wallet:', error);
      }
    };
    fileReader.readAsText(e.target.files[0]);
  };
  
  const handleUpload = async () => {
    if (!file || !wallet) return;
    
    try {
      setLoading(true);
      
      // Read file as array buffer
      const fileReader = new FileReader();
      fileReader.onload = async (e) => {
        try {
          const data = e.target.result;
          
          // Create transaction
          const transaction = await arweave.createTransaction({ data }, wallet);
          
          // Add tags
          transaction.addTag('Content-Type', file.type);
          transaction.addTag('App-Name', 'ArweaveUploader');
          
          // Sign transaction
          await arweave.transactions.sign(transaction, wallet);
          
          // Submit transaction
          await arweave.transactions.post(transaction);
          
          setTxId(transaction.id);
          setLoading(false);
        } catch (error) {
          console.error('Error uploading to Arweave:', error);
          setLoading(false);
        }
      };
      
      fileReader.readAsArrayBuffer(file);
    } catch (error) {
      console.error('Error uploading to Arweave:', error);
      setLoading(false);
    }
  };
  
  return (
    <div>
      <h2>Upload to Arweave</h2>
      
      <div>
        <h3>Step 1: Load Wallet</h3>
        <input type="file" accept=".json" onChange={handleWalletUpload} />
        {wallet && <p>Wallet loaded successfully!</p>}
      </div>
      
      <div>
        <h3>Step 2: Select File</h3>
        <input type="file" onChange={handleFileChange} />
      </div>
      
      <div>
        <h3>Step 3: Upload</h3>
        <button onClick={handleUpload} disabled={!file || !wallet || loading}>
          {loading ? 'Uploading...' : 'Upload to Arweave'}
        </button>
      </div>
      
      {txId && (
        <div>
          <h3>Upload Successful!</h3>
          <p>Transaction ID: {txId}</p>
          <p>
            View file: <a href={`https://arweave.net/${txId}`} target="_blank" rel="noopener noreferrer">
              https://arweave.net/{txId}
            </a>
          </p>
        </div>
      )}
    </div>
  );
}

export default ArweaveUploader;
```

## Ceramic Network

Ceramic is a decentralized data network for Web3 applications.

```bash
# Start Ceramic daemon
ceramic daemon

# Create a new document
ceramic create tile --content '{"hello": "world"}' --schema k3y52l7qbv1frxt706gqfzmq6cbqdkptzk8uudaryhlkf6ly9vx21hqu4r6k1jqio
```

Example JavaScript integration with Ceramic:

```javascript
const { CeramicClient } = require('@ceramicnetwork/http-client');
const { TileDocument } = require('@ceramicnetwork/stream-tile');
const { DID } = require('dids');
const { Ed25519Provider } = require('key-did-provider-ed25519');
const { getResolver } = require('key-did-resolver');
const { randomBytes } = require('crypto');

async function main() {
  // Connect to the Ceramic node
  const ceramic = new CeramicClient('http://ceramic:7007');
  
  // Create and authenticate a DID
  const seed = randomBytes(32);
  const provider = new Ed25519Provider(seed);
  const did = new DID({ provider, resolver: getResolver() });
  await did.authenticate();
  
  // Set the DID on the Ceramic client
  ceramic.did = did;
  
  // Create a new document
  const doc = await TileDocument.create(
    ceramic,
    {
      title: 'My First Document',
      content: 'Hello, Ceramic Network!',
      tags: ['example', 'ceramic'],
      createdAt: new Date().toISOString(),
    },
    {
      controllers: [did.id],
    }
  );
  
  console.log('Created document:', doc.id.toString());
  console.log('Content:', doc.content);
  
  // Update the document
  await doc.update({
    ...doc.content,
    updatedAt: new Date().toISOString(),
    content: 'Updated content on Ceramic Network!',
  });
  
  console.log('Updated document:', doc.content);
  
  // Load an existing document
  const loadedDoc = await TileDocument.load(ceramic, doc.id);
  console.log('Loaded document:', loadedDoc.content);
}

main().catch(console.error);
```

## OrbitDB

OrbitDB is a serverless, distributed, peer-to-peer database built on top of IPFS.

```bash
# Using OrbitDB with JavaScript
npm install orbit-db ipfs
```

Example JavaScript integration with OrbitDB:

```javascript
const IPFS = require('ipfs');
const OrbitDB = require('orbit-db');

async function main() {
  // Create an IPFS instance
  const ipfs = await IPFS.create({
    repo: './ipfs',
  });
  
  // Create an OrbitDB instance
  const orbitdb = await OrbitDB.createInstance(ipfs);
  
  // Create a database
  const db = await orbitdb.keyvalue('my-database');
  console.log('Database created:', db.address.toString());
  
  // Listen for database updates
  db.events.on('replicated', () => {
    console.log('Database replicated');
  });
  
  // Add entries to the database
  await db.put('name', 'Alice');
  await db.put('age', 30);
  await db.put('city', 'New York');
  
  // Get a value from the database
  const name = db.get('name');
  console.log('Name:', name);
  
  // Get all values from the database
  const all = db.all;
  console.log('All entries:', all);
  
  // Close the database and IPFS
  await db.close();
  await ipfs.stop();
}

main().catch(console.error);
```

## The Graph

The Graph is an indexing protocol for querying networks like Ethereum and IPFS.

```bash
# Install The Graph CLI
npm install -g @graphprotocol/graph-cli

# Initialize a new subgraph
graph init --from-example <subgraph-name>

# Generate code from subgraph manifest
graph codegen

# Build the subgraph
graph build

# Deploy the subgraph to a local Graph Node
graph deploy --node http://graph-node:8020/ --ipfs http://ipfs:5001/ <subgraph-name>
```

Example subgraph manifest:

```yaml
# subgraph.yaml
specVersion: 0.0.4
description: Example Subgraph
repository: https://github.com/example/subgraph
schema:
  file: ./schema.graphql
dataSources:
  - kind: ethereum/contract
    name: ExampleContract
    network: mainnet
    source:
      address: "0xContractAddress"
      abi: ExampleContract
      startBlock: 12345678
    mapping:
      kind: ethereum/events
      apiVersion: 0.0.6
      language: wasm/assemblyscript
      entities:
        - Transfer
      abis:
        - name: ExampleContract
          file: ./abis/ExampleContract.json
      eventHandlers:
        - event: Transfer(indexed address,indexed address,uint256)
          handler: handleTransfer
      file: ./src/mapping.ts
```

Example GraphQL schema:

```graphql
# schema.graphql
type Transfer @entity {
  id: ID!
  from: Bytes!
  to: Bytes!
  value: BigInt!
  timestamp: BigInt!
  block: BigInt!
  transaction: Bytes!
}
```

Example mapping file:

```typescript
// src/mapping.ts
import { Transfer as TransferEvent } from '../generated/ExampleContract/ExampleContract';
import { Transfer } from '../generated/schema';

export function handleTransfer(event: TransferEvent): void {
  let id = event.transaction.hash.toHexString() + '-' + event.logIndex.toString();
  let transfer = new Transfer(id);
  
  transfer.from = event.params.from;
  transfer.to = event.params.to;
  transfer.value = event.params.value;
  transfer.timestamp = event.block.timestamp;
  transfer.block = event.block.number;
  transfer.transaction = event.transaction.hash;
  
  transfer.save();
}
```

Example React component for querying The Graph:

```jsx
import React, { useState, useEffect } from 'react';
import { ApolloClient, InMemoryCache, gql } from '@apollo/client';

// Initialize Apollo Client
const client = new ApolloClient({
  uri: 'http://graph-node:8000/subgraphs/name/example/subgraph',
  cache: new InMemoryCache(),
});

function TransferList() {
  const [transfers, setTransfers] = useState([]);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    const fetchTransfers = async () => {
      try {
        const { data } = await client.query({
          query: gql`
            query GetRecentTransfers {
              transfers(first: 10, orderBy: timestamp, orderDirection: desc) {
                id
                from
                to
                value
                timestamp
                block
                transaction
              }
            }
          `,
        });
        
        setTransfers(data.transfers);
        setLoading(false);
      } catch (error) {
        console.error('Error fetching transfers:', error);
        setLoading(false);
      }
    };
    
    fetchTransfers();
  }, []);
  
  if (loading) return <p>Loading...</p>;
  
  return (
    <div>
      <h2>Recent Transfers</h2>
      <table>
        <thead>
          <tr>
            <th>From</th>
            <th>To</th>
            <th>Value</th>
            <th>Block</th>
          </tr>
        </thead>
        <tbody>
          {transfers.map((transfer) => (
            <tr key={transfer.id}>
              <td>{transfer.from}</td>
              <td>{transfer.to}</td>
              <td>{transfer.value}</td>
              <td>{transfer.block}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}

export default TransferList;