# Database Systems

This section covers various database systems and their integration.

## Table of Contents

- [Introduction](./USAGE-INTRO.md)
- [Blockchain Development](./USAGE-BLOCKCHAIN.md)
- [Layer 2 Development](./USAGE-LAYER2.md)
- [Cross-Chain Development](./USAGE-CROSSCHAIN.md)
- [Smart Wallet Integration](./USAGE-WALLETS.md)
- [Frontend Development](./USAGE-FRONTEND.md)
- [Decentralized Storage](./USAGE-STORAGE.md)
- [Database Systems](#database-systems) (this file)
- [Advanced Cryptography](./USAGE-CRYPTO.md)
- [WebXR/AR/VR Development](./USAGE-WEBXR.md)
- [CI/CD Integration](./USAGE-CICD.md)

## PostgreSQL (SQL Database)

PostgreSQL is a powerful, open-source object-relational database system.

```bash
# Connect to PostgreSQL using the convenience script
connect-postgres

# Or connect manually
psql -h postgres -U postgres -d postgres

# Create a new database
CREATE DATABASE myapp;
\c myapp

# Create a table
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  username VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

# Insert data
INSERT INTO users (username, email) VALUES ('alice', 'alice@example.com');
INSERT INTO users (username, email) VALUES ('bob', 'bob@example.com');

# Query data
SELECT * FROM users;
```

Example Node.js integration with PostgreSQL:

```javascript
const { Pool } = require('pg');

// Create a connection pool
const pool = new Pool({
  host: 'postgres',
  user: 'postgres',
  password: 'postgres',
  database: 'myapp',
  port: 5432,
});

async function main() {
  // Create a table
  await pool.query(`
    CREATE TABLE IF NOT EXISTS users (
      id SERIAL PRIMARY KEY,
      username VARCHAR(100) NOT NULL,
      email VARCHAR(100) UNIQUE NOT NULL,
      created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    )
  `);
  
  // Insert a user
  const insertResult = await pool.query(
    'INSERT INTO users(username, email) VALUES($1, $2) RETURNING *',
    ['alice', 'alice@example.com']
  );
  console.log('Inserted user:', insertResult.rows[0]);
  
  // Query users
  const queryResult = await pool.query('SELECT * FROM users');
  console.log('All users:', queryResult.rows);
  
  // Update a user
  const updateResult = await pool.query(
    'UPDATE users SET username = $1 WHERE email = $2 RETURNING *',
    ['alice_updated', 'alice@example.com']
  );
  console.log('Updated user:', updateResult.rows[0]);
  
  // Delete a user
  const deleteResult = await pool.query(
    'DELETE FROM users WHERE email = $1 RETURNING *',
    ['alice@example.com']
  );
  console.log('Deleted user:', deleteResult.rows[0]);
  
  // Close the pool
  await pool.end();
}

main().catch(console.error);
```

Example Python integration with PostgreSQL:

```python
import psycopg2
from psycopg2.extras import RealDictCursor

# Connect to PostgreSQL
conn = psycopg2.connect(
    host="postgres",
    database="postgres",
    user="postgres",
    password="postgres"
)

# Create a cursor
cur = conn.cursor(cursor_factory=RealDictCursor)

# Create a database
cur.execute("CREATE DATABASE IF NOT EXISTS myapp")
conn.commit()

# Connect to the new database
conn.close()
conn = psycopg2.connect(
    host="postgres",
    database="myapp",
    user="postgres",
    password="postgres"
)
cur = conn.cursor(cursor_factory=RealDictCursor)

# Create a table
cur.execute("""
    CREATE TABLE IF NOT EXISTS users (
        id SERIAL PRIMARY KEY,
        username VARCHAR(100) NOT NULL,
        email VARCHAR(100) UNIQUE NOT NULL,
        created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    )
""")
conn.commit()

# Insert data
cur.execute(
    "INSERT INTO users (username, email) VALUES (%s, %s) RETURNING *",
    ("alice", "alice@example.com")
)
inserted_user = cur.fetchone()
print("Inserted user:", inserted_user)
conn.commit()

# Query data
cur.execute("SELECT * FROM users")
users = cur.fetchall()
print("All users:", users)

# Update data
cur.execute(
    "UPDATE users SET username = %s WHERE email = %s RETURNING *",
    ("alice_updated", "alice@example.com")
)
updated_user = cur.fetchone()
print("Updated user:", updated_user)
conn.commit()

# Delete data
cur.execute(
    "DELETE FROM users WHERE email = %s RETURNING *",
    ("alice@example.com",)
)
deleted_user = cur.fetchone()
print("Deleted user:", deleted_user)
conn.commit()

# Close the connection
cur.close()
conn.close()
```

## MongoDB (NoSQL Database)

MongoDB is a document-oriented NoSQL database.

```bash
# Connect to MongoDB using the convenience script
connect-mongodb

# Or connect manually
mongo mongodb://admin:adminpassword@mongodb:27017

# Create a new database
use myapp

# Create a collection and insert a document
db.users.insertOne({
  username: "johndoe",
  email: "john@example.com",
  profile: {
    firstName: "John",
    lastName: "Doe"
  },
  tags: ["developer", "blockchain"],
  createdAt: new Date()
})

# Query documents
db.users.find()

# Query with filter
db.users.find({ username: "johndoe" })

# Update a document
db.users.updateOne(
  { username: "johndoe" },
  { $set: { "profile.firstName": "Johnny" } }
)

# Delete a document
db.users.deleteOne({ username: "johndoe" })
```

Example Node.js integration with MongoDB:

```javascript
const { MongoClient } = require('mongodb');

// Connection URI
const uri = 'mongodb://admin:adminpassword@mongodb:27017';
const client = new MongoClient(uri);

async function main() {
  try {
    // Connect to MongoDB
    await client.connect();
    console.log('Connected to MongoDB');
    
    // Get database and collection
    const database = client.db('myapp');
    const users = database.collection('users');
    
    // Insert a document
    const insertResult = await users.insertOne({
      username: 'johndoe',
      email: 'john@example.com',
      profile: {
        firstName: 'John',
        lastName: 'Doe'
      },
      tags: ['developer', 'blockchain'],
      createdAt: new Date()
    });
    console.log('Inserted document ID:', insertResult.insertedId);
    
    // Find documents
    const findResult = await users.find({}).toArray();
    console.log('Found documents:', findResult);
    
    // Find a specific document
    const findOneResult = await users.findOne({ username: 'johndoe' });
    console.log('Found specific document:', findOneResult);
    
    // Update a document
    const updateResult = await users.updateOne(
      { username: 'johndoe' },
      { $set: { 'profile.firstName': 'Johnny' } }
    );
    console.log('Updated document count:', updateResult.modifiedCount);
    
    // Delete a document
    const deleteResult = await users.deleteOne({ username: 'johndoe' });
    console.log('Deleted document count:', deleteResult.deletedCount);
    
  } finally {
    // Close the connection
    await client.close();
    console.log('Disconnected from MongoDB');
  }
}

main().catch(console.error);
```

Example Python integration with MongoDB:

```python
from pymongo import MongoClient
from datetime import datetime

# Connect to MongoDB
client = MongoClient('mongodb://admin:adminpassword@mongodb:27017/')
db = client['myapp']
users = db['users']

# Insert a document
insert_result = users.insert_one({
    'username': 'johndoe',
    'email': 'john@example.com',
    'profile': {
        'firstName': 'John',
        'lastName': 'Doe'
    },
    'tags': ['developer', 'blockchain'],
    'createdAt': datetime.now()
})
print('Inserted document ID:', insert_result.inserted_id)

# Find documents
find_result = list(users.find({}))
print('Found documents:', find_result)

# Find a specific document
find_one_result = users.find_one({'username': 'johndoe'})
print('Found specific document:', find_one_result)

# Update a document
update_result = users.update_one(
    {'username': 'johndoe'},
    {'$set': {'profile.firstName': 'Johnny'}}
)
print('Updated document count:', update_result.modified_count)

# Delete a document
delete_result = users.delete_one({'username': 'johndoe'})
print('Deleted document count:', delete_result.deleted_count)

# Close the connection
client.close()
```

## Neo4j (Graph Database)

Neo4j is a graph database management system.

```bash
# Connect to Neo4j using the convenience script
connect-neo4j

# Or connect manually
cypher-shell -a neo4j:7687 -u neo4j -p password

# Create nodes and relationships
CREATE (a:Person {name: 'Alice', age: 30})
CREATE (b:Person {name: 'Bob', age: 32})
CREATE (a)-[:KNOWS {since: 2020}]->(b)
RETURN a, b;

# Query nodes
MATCH (p:Person) RETURN p;

# Query relationships
MATCH (a:Person)-[r:KNOWS]->(b:Person) RETURN a, r, b;

# Update nodes
MATCH (p:Person {name: 'Alice'})
SET p.age = 31
RETURN p;

# Delete nodes and relationships
MATCH (p:Person {name: 'Alice'})
DETACH DELETE p;
```

Example Node.js integration with Neo4j:

```javascript
const neo4j = require('neo4j-driver');

// Create a driver instance
const driver = neo4j.driver(
  'neo4j://neo4j:7687',
  neo4j.auth.basic('neo4j', 'password')
);

async function main() {
  // Create a session
  const session = driver.session();
  
  try {
    // Create nodes and relationships
    const createResult = await session.run(`
      CREATE (a:Person {name: $nameA, age: $ageA})
      CREATE (b:Person {name: $nameB, age: $ageB})
      CREATE (a)-[r:KNOWS {since: $since}]->(b)
      RETURN a, b, r
    `, {
      nameA: 'Alice',
      ageA: 30,
      nameB: 'Bob',
      ageB: 32,
      since: 2020
    });
    
    console.log('Created nodes and relationship:');
    const record = createResult.records[0];
    console.log('Alice:', record.get('a').properties);
    console.log('Bob:', record.get('b').properties);
    console.log('Relationship:', record.get('r').properties);
    
    // Query nodes
    const queryNodesResult = await session.run(`
      MATCH (p:Person)
      RETURN p.name AS name, p.age AS age
    `);
    
    console.log('All persons:');
    queryNodesResult.records.forEach(record => {
      console.log(`${record.get('name')}, ${record.get('age')} years old`);
    });
    
    // Query relationships
    const queryRelationshipsResult = await session.run(`
      MATCH (a:Person)-[r:KNOWS]->(b:Person)
      RETURN a.name AS from, b.name AS to, r.since AS since
    `);
    
    console.log('Relationships:');
    queryRelationshipsResult.records.forEach(record => {
      console.log(`${record.get('from')} knows ${record.get('to')} since ${record.get('since')}`);
    });
    
    // Update a node
    const updateResult = await session.run(`
      MATCH (p:Person {name: $name})
      SET p.age = $age
      RETURN p
    `, {
      name: 'Alice',
      age: 31
    });
    
    console.log('Updated Alice:', updateResult.records[0].get('p').properties);
    
    // Delete nodes and relationships
    const deleteResult = await session.run(`
      MATCH (p:Person {name: $name})
      DETACH DELETE p
      RETURN count(p) AS deleted
    `, {
      name: 'Alice'
    });
    
    console.log('Deleted nodes:', deleteResult.records[0].get('deleted').toInt());
    
  } finally {
    // Close the session
    await session.close();
  }
  
  // Close the driver
  await driver.close();
}

main().catch(console.error);
```

Example Python integration with Neo4j:

```python
from neo4j import GraphDatabase

# Create a driver instance
driver = GraphDatabase.driver("neo4j://neo4j:7687", auth=("neo4j", "password"))

# Create nodes and relationships
def create_nodes_and_relationship(tx, name_a, age_a, name_b, age_b, since):
    result = tx.run("""
        CREATE (a:Person {name: $nameA, age: $ageA})
        CREATE (b:Person {name: $nameB, age: $ageB})
        CREATE (a)-[r:KNOWS {since: $since}]->(b)
        RETURN a, b, r
    """, nameA=name_a, ageA=age_a, nameB=name_b, ageB=age_b, since=since)
    record = result.single()
    return {
        "a": record["a"],
        "b": record["b"],
        "r": record["r"]
    }

# Query nodes
def get_all_persons(tx):
    result = tx.run("""
        MATCH (p:Person)
        RETURN p.name AS name, p.age AS age
    """)
    return [{"name": record["name"], "age": record["age"]} for record in result]

# Query relationships
def get_relationships(tx):
    result = tx.run("""
        MATCH (a:Person)-[r:KNOWS]->(b:Person)
        RETURN a.name AS from, b.name AS to, r.since AS since
    """)
    return [{"from": record["from"], "to": record["to"], "since": record["since"]} for record in result]

# Update a node
def update_person_age(tx, name, age):
    result = tx.run("""
        MATCH (p:Person {name: $name})
        SET p.age = $age
        RETURN p
    """, name=name, age=age)
    record = result.single()
    return record["p"] if record else None

# Delete nodes and relationships
def delete_person(tx, name):
    result = tx.run("""
        MATCH (p:Person {name: $name})
        DETACH DELETE p
        RETURN count(p) AS deleted
    """, name=name)
    record = result.single()
    return record["deleted"]

# Execute the functions
with driver.session() as session:
    # Create nodes and relationship
    created = session.write_transaction(
        create_nodes_and_relationship,
        "Alice", 30, "Bob", 32, 2020
    )
    print("Created nodes and relationship:")
    print("Alice:", created["a"])
    print("Bob:", created["b"])
    print("Relationship:", created["r"])
    
    # Query nodes
    persons = session.read_transaction(get_all_persons)
    print("All persons:")
    for person in persons:
        print(f"{person['name']}, {person['age']} years old")
    
    # Query relationships
    relationships = session.read_transaction(get_relationships)
    print("Relationships:")
    for rel in relationships:
        print(f"{rel['from']} knows {rel['to']} since {rel['since']}")
    
    # Update a node
    updated = session.write_transaction(update_person_age, "Alice", 31)
    print("Updated Alice:", updated)
    
    # Delete nodes and relationships
    deleted = session.write_transaction(delete_person, "Alice")
    print("Deleted nodes:", deleted)

# Close the driver
driver.close()
```

## Milvus (Vector Database)

Milvus is an open-source vector database for similarity search and AI applications.

```bash
# Connect to Milvus using the convenience script
connect-milvus

# Using with Python
pip install pymilvus
```

Example Python integration with Milvus:

```python
from pymilvus import connections, Collection, FieldSchema, CollectionSchema, DataType, utility
import numpy as np

# Connect to Milvus
connections.connect(host="milvus", port="19530")

# Create a collection
def create_collection(collection_name, dimension):
    if utility.has_collection(collection_name):
        utility.drop_collection(collection_name)
    
    fields = [
        FieldSchema(name="id", dtype=DataType.INT64, is_primary=True, auto_id=True),
        FieldSchema(name="embedding", dtype=DataType.FLOAT_VECTOR, dim=dimension),
        FieldSchema(name="text", dtype=DataType.VARCHAR, max_length=200)
    ]
    
    schema = CollectionSchema(fields=fields, description=f"{collection_name} collection")
    collection = Collection(name=collection_name, schema=schema)
    
    # Create an index
    index_params = {
        "metric_type": "L2",
        "index_type": "IVF_FLAT",
        "params": {"nlist": 128}
    }
    collection.create_index(field_name="embedding", index_params=index_params)
    
    return collection

# Insert vectors
def insert_vectors(collection, vectors, texts):
    entities = [
        vectors,
        texts
    ]
    collection.insert(entities)
    collection.flush()
    return collection.num_entities

# Search vectors
def search_vectors(collection, query_vectors, top_k=5):
    collection.load()
    search_params = {"metric_type": "L2", "params": {"nprobe": 10}}
    results = collection.search(
        data=query_vectors,
        anns_field="embedding",
        param=search_params,
        limit=top_k,
        output_fields=["text"]
    )
    return results

# Main function
def main():
    # Parameters
    collection_name = "text_embeddings"
    dimension = 128
    num_vectors = 10000
    
    # Create collection
    collection = create_collection(collection_name, dimension)
    print(f"Collection '{collection_name}' created")
    
    # Generate random vectors and texts
    vectors = np.random.random((num_vectors, dimension)).tolist()
    texts = [f"text_{i}" for i in range(num_vectors)]
    
    # Insert vectors
    num_entities = insert_vectors(collection, vectors, texts)
    print(f"Inserted {num_entities} entities")
    
    # Generate query vectors
    query_vectors = np.random.random((5, dimension)).tolist()
    
    # Search vectors
    results = search_vectors(collection, query_vectors)
    
    # Print results
    for i, result in enumerate(results):
        print(f"Query {i}, top {len(result)} results:")
        for j, entity in enumerate(result):
            print(f"  {j}. ID: {entity.id}, Distance: {entity.distance}, Text: {entity.entity.get('text')}")

if __name__ == "__main__":
    main()
```

Example use case: Semantic search with Milvus and sentence-transformers:

```python
from pymilvus import connections, Collection, FieldSchema, CollectionSchema, DataType, utility
from sentence_transformers import SentenceTransformer
import numpy as np

# Connect to Milvus
connections.connect(host="milvus", port="19530")

# Load sentence transformer model
model = SentenceTransformer('all-MiniLM-L6-v2')

# Create a collection
def create_collection(collection_name):
    if utility.has_collection(collection_name):
        utility.drop_collection(collection_name)
    
    fields = [
        FieldSchema(name="id", dtype=DataType.INT64, is_primary=True, auto_id=True),
        FieldSchema(name="embedding", dtype=DataType.FLOAT_VECTOR, dim=384),  # Model dimension
        FieldSchema(name="text", dtype=DataType.VARCHAR, max_length=2000)
    ]
    
    schema = CollectionSchema(fields=fields, description=f"{collection_name} collection")
    collection = Collection(name=collection_name, schema=schema)
    
    # Create an index
    index_params = {
        "metric_type": "COSINE",  # Use cosine similarity for semantic search
        "index_type": "IVF_FLAT",
        "params": {"nlist": 128}
    }
    collection.create_index(field_name="embedding", index_params=index_params)
    
    return collection

# Insert documents
def insert_documents(collection, documents):
    # Generate embeddings
    embeddings = model.encode(documents)
    
    # Insert into Milvus
    entities = [
        embeddings.tolist(),
        documents
    ]
    collection.insert(entities)
    collection.flush()
    return collection.num_entities

# Search documents
def search_documents(collection, query, top_k=5):
    # Generate query embedding
    query_embedding = model.encode([query])[0].tolist()
    
    # Search in Milvus
    collection.load()
    search_params = {"metric_type": "COSINE", "params": {"nprobe": 10}}
    results = collection.search(
        data=[query_embedding],
        anns_field="embedding",
        param=search_params,
        limit=top_k,
        output_fields=["text"]
    )
    return results[0]  # Return results for the first query

# Main function
def main():
    # Create collection
    collection_name = "semantic_search"
    collection = create_collection(collection_name)
    print(f"Collection '{collection_name}' created")
    
    # Sample documents
    documents = [
        "Ethereum is a decentralized blockchain platform that establishes a peer-to-peer network.",
        "Bitcoin is a cryptocurrency, a virtual currency designed to act as money.",
        "Solana is a highly functional open source project that implements a new, permissionless blockchain.",
        "Polkadot is a platform that allows diverse blockchains to transfer messages and value.",
        "Cardano is a proof-of-stake blockchain platform that says its goal is to allow changemakers.",
        "Avalanche is an open-source platform for launching decentralized applications.",
        "Cosmos is a decentralized network of independent parallel blockchains.",
        "Tezos is a blockchain network linked to a digital token, which is called a tez or a tezzie.",
        "Near Protocol is a layer-one blockchain designed as a community-run cloud computing platform.",
        "Algorand is a blockchain cryptocurrency protocol that aims to be simultaneously scalable, secure, and decentralized."
    ]
    
    # Insert documents
    num_entities = insert_documents(collection, documents)
    print(f"Inserted {num_entities} documents")
    
    # Search for similar documents
    query = "Which blockchain is focused on scalability and performance?"
    results = search_documents(collection, query)
    
    # Print results
    print(f"Query: '{query}'")
    print("Results:")
    for i, result in enumerate(results):
        print(f"  {i+1}. Score: {result.distance:.4f}")
        print(f"     Text: {result.entity.get('text')}")

if __name__ == "__main__":
    main()
```

## Database Integration in Web3 Applications

### Storing Off-Chain Data for NFTs

```javascript
// Example: Storing NFT metadata in MongoDB
const { MongoClient } = require('mongodb');
const { ethers } = require('ethers');

// NFT contract ABI (simplified)
const nftAbi = [
  "event Transfer(address indexed from, address indexed to, uint256 indexed tokenId)",
  "function tokenURI(uint256 tokenId) view returns (string)"
];

async function main() {
  // Connect to MongoDB
  const mongoClient = new MongoClient('mongodb://admin:adminpassword@mongodb:27017');
  await mongoClient.connect();
  const db = mongoClient.db('nft-metadata');
  const collection = db.collection('metadata');
  
  // Connect to Ethereum
  const provider = new ethers.providers.JsonRpcProvider('http://geth:8545');
  const nftContract = new ethers.Contract('0xNFTContractAddress', nftAbi, provider);
  
  // Listen for Transfer events
  nftContract.on('Transfer', async (from, to, tokenId, event) => {
    console.log(`NFT Transfer: Token ID ${tokenId} from ${from} to ${to}`);
    
    // Get token URI
    const tokenURI = await nftContract.tokenURI(tokenId);
    
    // Fetch metadata from IPFS or other source
    const metadata = await fetchMetadata(tokenURI);
    
    // Store in MongoDB
    await collection.updateOne(
      { tokenId: tokenId.toString() },
      { 
        $set: { 
          tokenId: tokenId.toString(),
          owner: to,
          tokenURI,
          metadata,
          lastTransfer: new Date(),
          blockNumber: event.blockNumber,
          transactionHash: event.transactionHash
        } 
      },
      { upsert: true }
    );
    
    console.log(`Metadata for token ID ${tokenId} stored in MongoDB`);
  });
  
  console.log('Listening for NFT transfers...');
}

async function fetchMetadata(tokenURI) {
  // If IPFS URI
  if (tokenURI.startsWith('ipfs://')) {
    const ipfsHash = tokenURI.replace('ipfs://', '');
    const response = await fetch(`http://ipfs:8080/ipfs/${ipfsHash}`);
    return response.json();
  }
  
  // If HTTP URI
  if (tokenURI.startsWith('http')) {
    const response = await fetch(tokenURI);
    return response.json();
  }
  
  return { error: 'Unsupported token URI format' };
}

main().catch(console.error);
```

### Indexing Blockchain Events

```javascript
// Example: Indexing blockchain events in PostgreSQL
const { Pool } = require('pg');
const { ethers } = require('ethers');

// ERC-20 contract ABI (simplified)
const erc20Abi = [
  "event Transfer(address indexed from, address indexed to, uint256 value)",
  "function name() view returns (string)",
  "function symbol() view returns (string)",
  "function decimals() view returns (uint8)"
];

async function main() {
  // Connect to PostgreSQL
  const pool = new Pool({
    host: 'postgres',
    user: 'postgres',
    password: 'postgres',
    database: 'blockchain_events',
    port: 5432,
  });
  
  // Create tables
  await pool.query(`
    CREATE TABLE IF NOT EXISTS tokens (
      address TEXT PRIMARY KEY,
      name TEXT NOT NULL,
      symbol TEXT NOT NULL,
      decimals INTEGER NOT NULL
    )
  `);
  
  await pool.query(`
    CREATE TABLE IF NOT EXISTS transfers (
      id SERIAL PRIMARY KEY,
      token_address TEXT NOT NULL REFERENCES tokens(address),
      from_address TEXT NOT NULL,
      to_address TEXT NOT NULL,
      value NUMERIC NOT NULL,
      block_number INTEGER NOT NULL,
      transaction_hash TEXT NOT NULL,
      log_index INTEGER NOT NULL,
      timestamp TIMESTAMP NOT NULL,
      UNIQUE(transaction_hash, log_index)
    )
  `);
  
  // Connect to Ethereum
  const provider = new ethers.providers.JsonRpcProvider('http://geth:8545');
  
  // Token contract address
  const tokenAddress = '0xTokenContractAddress';
  const tokenContract = new ethers.Contract(tokenAddress, erc20Abi, provider);
  
  // Get token details
  const name = await tokenContract.name();
  const symbol = await tokenContract.symbol();
  const decimals = await tokenContract.decimals();
  
  // Store token details
  await pool.query(
    'INSERT INTO tokens(address, name, symbol, decimals) VALUES($1, $2, $3, $4) ON CONFLICT (address) DO NOTHING',
    [tokenAddress, name, symbol, decimals]
  );
  
  console.log(`Indexed token: ${name} (${symbol})`);
  
  // Listen for Transfer events
  tokenContract.on('Transfer', async (from, to, value, event) => {
    const block = await provider.getBlock(event.blockNumber);
    
    // Store transfer event
    await pool.query(
      `INSERT INTO transfers(
        token_address, from_address, to_address, value, 
        block_number, transaction_hash, log_index, timestamp
      ) VALUES($1, $2, $3, $4, $5, $6, $7, $8)
      ON CONFLICT(transaction_hash, log_index) DO NOTHING`,
      [
        tokenAddress,
        from,
        to,
        value.toString(),
        event.blockNumber,
        event.transactionHash,
        event.logIndex,
        new Date(block.timestamp * 1000)
      ]
    );
    
    console.log(`Indexed transfer: ${from} -> ${to}, ${ethers.utils.formatUnits(value, decimals)} ${symbol}`);
  });
  
  console.log('Listening for token transfers...');
}

main().catch(console.error);