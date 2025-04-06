# Advanced Cryptography

This section covers advanced cryptography techniques and tools.

## Table of Contents

- [Introduction](./USAGE-INTRO.md)
- [Blockchain Development](./USAGE-BLOCKCHAIN.md)
- [Layer 2 Development](./USAGE-LAYER2.md)
- [Cross-Chain Development](./USAGE-CROSSCHAIN.md)
- [Smart Wallet Integration](./USAGE-WALLETS.md)
- [Frontend Development](./USAGE-FRONTEND.md)
- [Decentralized Storage](./USAGE-STORAGE.md)
- [Database Systems](./USAGE-DATABASES.md)
- [Advanced Cryptography](#advanced-cryptography) (this file)
- [WebXR/AR/VR Development](./USAGE-WEBXR.md)
- [CI/CD Integration](./USAGE-CICD.md)

## Post-Quantum Cryptography

Post-quantum cryptography refers to cryptographic algorithms that are secure against attacks by quantum computers.

### Kyber (Key Encapsulation)

Kyber is a key encapsulation mechanism that is secure against quantum computer attacks.

```bash
# Use Kyber for key encapsulation
cd /opt/kyber/ref
./test_kyber
```

Example C code for Kyber:

```c
#include "api.h"
#include "randombytes.h"
#include <stdio.h>
#include <string.h>

int main(void) {
    uint8_t pk[CRYPTO_PUBLICKEYBYTES];
    uint8_t sk[CRYPTO_SECRETKEYBYTES];
    uint8_t ct[CRYPTO_CIPHERTEXTBYTES];
    uint8_t key_a[CRYPTO_BYTES];
    uint8_t key_b[CRYPTO_BYTES];
    
    // Key generation
    crypto_kem_keypair(pk, sk);
    
    // Encapsulation
    crypto_kem_enc(ct, key_b, pk);
    
    // Decapsulation
    crypto_kem_dec(key_a, ct, sk);
    
    // Check if shared keys match
    if (memcmp(key_a, key_b, CRYPTO_BYTES) == 0) {
        printf("Shared keys match. Success!\n");
    } else {
        printf("Shared keys do not match. Failure!\n");
    }
    
    return 0;
}
```

### Dilithium (Digital Signatures)

Dilithium is a digital signature scheme that is secure against quantum computer attacks.

```bash
# Use Dilithium for signatures
cd /opt/dilithium/ref
./test_dilithium
```

Example C code for Dilithium:

```c
#include "api.h"
#include "randombytes.h"
#include <stdio.h>
#include <string.h>

int main(void) {
    uint8_t pk[CRYPTO_PUBLICKEYBYTES];
    uint8_t sk[CRYPTO_SECRETKEYBYTES];
    uint8_t sig[CRYPTO_BYTES];
    uint8_t msg[100] = "This is a test message for Dilithium signature.";
    unsigned long long siglen;
    
    // Key generation
    crypto_sign_keypair(pk, sk);
    
    // Sign message
    crypto_sign_signature(sig, &siglen, msg, strlen((char*)msg), sk);
    
    // Verify signature
    if (crypto_sign_verify(sig, siglen, msg, strlen((char*)msg), pk) == 0) {
        printf("Signature verified. Success!\n");
    } else {
        printf("Signature verification failed!\n");
    }
    
    return 0;
}
```

### liboqs (Open Quantum Safe)

liboqs is an open-source C library for quantum-resistant cryptographic algorithms.

```bash
# Compile and run a liboqs example
cd /opt/liboqs/build/tests
./example_kem
```

Example C code using liboqs:

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <oqs/oqs.h>

int main(void) {
    OQS_STATUS rc;
    uint8_t *public_key = NULL;
    uint8_t *secret_key = NULL;
    uint8_t *ciphertext = NULL;
    uint8_t *shared_secret_e = NULL;
    uint8_t *shared_secret_d = NULL;
    
    // Enable the desired algorithm
    const char *kem_name = "Kyber512";
    OQS_KEM *kem = OQS_KEM_new(kem_name);
    if (kem == NULL) {
        printf("KEM algorithm %s not supported\n", kem_name);
        return EXIT_FAILURE;
    }
    
    // Allocate memory
    public_key = malloc(kem->length_public_key);
    secret_key = malloc(kem->length_secret_key);
    ciphertext = malloc(kem->length_ciphertext);
    shared_secret_e = malloc(kem->length_shared_secret);
    shared_secret_d = malloc(kem->length_shared_secret);
    
    // Key generation
    rc = OQS_KEM_keypair(kem, public_key, secret_key);
    if (rc != OQS_SUCCESS) {
        fprintf(stderr, "Key generation failed\n");
        return EXIT_FAILURE;
    }
    
    // Encapsulation
    rc = OQS_KEM_encaps(kem, ciphertext, shared_secret_e, public_key);
    if (rc != OQS_SUCCESS) {
        fprintf(stderr, "Encapsulation failed\n");
        return EXIT_FAILURE;
    }
    
    // Decapsulation
    rc = OQS_KEM_decaps(kem, shared_secret_d, ciphertext, secret_key);
    if (rc != OQS_SUCCESS) {
        fprintf(stderr, "Decapsulation failed\n");
        return EXIT_FAILURE;
    }
    
    // Check if shared secrets match
    if (memcmp(shared_secret_e, shared_secret_d, kem->length_shared_secret) == 0) {
        printf("Shared secrets match. Success!\n");
    } else {
        printf("Shared secrets do not match. Failure!\n");
    }
    
    // Clean up
    OQS_KEM_free(kem);
    free(public_key);
    free(secret_key);
    free(ciphertext);
    free(shared_secret_e);
    free(shared_secret_d);
    
    return EXIT_SUCCESS;
}
```

## Fully Homomorphic Encryption (FHE)

Fully Homomorphic Encryption allows computations on encrypted data without decrypting it.

### Microsoft SEAL

SEAL is a homomorphic encryption library developed by Microsoft.

```bash
# Example using SEAL
cd /opt/SEAL/build/bin
./sealexamples
```

Example C++ code using SEAL:

```cpp
#include <iostream>
#include <vector>
#include "seal/seal.h"

using namespace std;
using namespace seal;

int main() {
    // Set up encryption parameters
    EncryptionParameters parms(scheme_type::bfv);
    size_t poly_modulus_degree = 4096;
    parms.set_poly_modulus_degree(poly_modulus_degree);
    parms.set_coeff_modulus(CoeffModulus::BFVDefault(poly_modulus_degree));
    parms.set_plain_modulus(PlainModulus::Batching(poly_modulus_degree, 20));
    
    // Generate context
    SEALContext context(parms);
    
    // Generate keys
    KeyGenerator keygen(context);
    SecretKey secret_key = keygen.secret_key();
    PublicKey public_key;
    keygen.create_public_key(public_key);
    
    // Create encryptor, evaluator, and decryptor
    Encryptor encryptor(context, public_key);
    Evaluator evaluator(context);
    Decryptor decryptor(context, secret_key);
    
    // Create batch encoder
    BatchEncoder batch_encoder(context);
    size_t slot_count = batch_encoder.slot_count();
    size_t row_size = slot_count / 2;
    
    // Create data for encryption
    vector<uint64_t> pod_matrix(slot_count, 0ULL);
    for (size_t i = 0; i < slot_count; i++) {
        pod_matrix[i] = i;
    }
    
    // Encode and encrypt
    Plaintext plain_matrix;
    batch_encoder.encode(pod_matrix, plain_matrix);
    Ciphertext encrypted_matrix;
    encryptor.encrypt(plain_matrix, encrypted_matrix);
    
    // Perform homomorphic operations (e.g., add a constant)
    Plaintext plain_scalar;
    batch_encoder.encode(vector<uint64_t>(slot_count, 5ULL), plain_scalar);
    evaluator.add_plain_inplace(encrypted_matrix, plain_scalar);
    
    // Decrypt and decode
    Plaintext plain_result;
    decryptor.decrypt(encrypted_matrix, plain_result);
    vector<uint64_t> pod_result;
    batch_encoder.decode(plain_result, pod_result);
    
    // Print results
    cout << "Original values: ";
    for (size_t i = 0; i < 10; i++) {
        cout << pod_matrix[i] << " ";
    }
    cout << "..." << endl;
    
    cout << "Result (original + 5): ";
    for (size_t i = 0; i < 10; i++) {
        cout << pod_result[i] << " ";
    }
    cout << "..." << endl;
    
    return 0;
}
```

### HElib

HElib is an open-source library for homomorphic encryption.

```bash
# Compile and run a HElib example
cd /opt/HElib/examples
g++ -o simple_example simple_example.cpp -L/usr/local/lib -lhelib -lntl -lgmp -pthread
./simple_example
```

Example C++ code using HElib:

```cpp
#include <iostream>
#include <helib/helib.h>

using namespace std;
using namespace helib;

int main() {
    // Initialize context
    long p = 2;         // Plaintext prime modulus
    long r = 1;         // Lifting
    long d = 1;         // Degree of field extension
    long c = 2;         // Number of columns in key-switching matrix
    long bits = 64;     // Precision
    long L = 16;        // Number of levels in modulus chain
    
    Context context = ContextBuilder<BGV>()
        .m(4095)
        .p(p)
        .r(r)
        .bits(bits)
        .c(c)
        .build();
    
    // Create secret key
    SecKey secret_key(context);
    secret_key.GenSecKey();
    
    // Create public key (implicit in secret key)
    const PubKey& public_key = secret_key;
    
    // Create encryptor, evaluator, and decryptor
    Encryptor encryptor(public_key);
    Evaluator evaluator(public_key);
    Decryptor decryptor(secret_key);
    
    // Create plaintext
    vector<long> data = {1, 2, 3, 4, 5};
    Ptxt<BGV> plaintext(context);
    for (size_t i = 0; i < data.size(); i++) {
        plaintext[i] = data[i];
    }
    
    // Encrypt
    Ctxt<BGV> ciphertext(public_key);
    encryptor.encrypt(ciphertext, plaintext);
    
    // Perform homomorphic operations (e.g., multiply by 2)
    Ptxt<BGV> scalar(context);
    scalar[0] = 2;
    evaluator.multByConstant(ciphertext, scalar);
    
    // Decrypt
    Ptxt<BGV> result(context);
    decryptor.decrypt(ciphertext, result);
    
    // Print results
    cout << "Original values: ";
    for (auto val : data) {
        cout << val << " ";
    }
    cout << endl;
    
    cout << "Result (original * 2): ";
    for (size_t i = 0; i < data.size(); i++) {
        cout << result[i] << " ";
    }
    cout << endl;
    
    return 0;
}
```

### Python with TenSEAL

TenSEAL is a library for homomorphic encryption in Python.

```python
# Python example with TenSEAL
import tenseal as ts

# Create TenSEAL context
context = ts.context(
    ts.SCHEME_TYPE.CKKS,
    poly_modulus_degree=8192,
    coeff_mod_bit_sizes=[60, 40, 40, 60]
)
context.global_scale = 2**40
context.generate_galois_keys()

# Encrypt vectors
v1 = ts.ckks_vector(context, [1, 2, 3, 4])
v2 = ts.ckks_vector(context, [5, 6, 7, 8])

# Perform operations on encrypted data
result_add = v1 + v2
result_mul = v1 * 5

# Decrypt results
print("v1 =", v1.decrypt())
print("v2 =", v2.decrypt())
print("v1 + v2 =", result_add.decrypt())
print("v1 * 5 =", result_mul.decrypt())
```

## Multi-Party Computation (MPC)

Multi-Party Computation allows multiple parties to jointly compute a function over their inputs while keeping those inputs private.

### MP-SPDZ

MP-SPDZ is a framework for multi-party computation.

```bash
# Example using MP-SPDZ
cd /opt/MP-SPDZ
./compile.py tutorial
./Scripts/setup-online.sh
./Scripts/tutorial.sh
```

Example MP-SPDZ program (tutorial.mpc):

```python
# Define inputs for each party
a = sint.get_input_from(0)
b = sint.get_input_from(1)

# Compute sum and product
c = a + b
d = a * b

# Reveal results
print_ln('a + b = %s', c.reveal())
print_ln('a * b = %s', d.reveal())
```

Running the example:

```bash
# Terminal 1 (Party 0)
cd /opt/MP-SPDZ
./Player-Online.x -N 2 -p 0 tutorial

# Terminal 2 (Party 1)
cd /opt/MP-SPDZ
./Player-Online.x -N 2 -p 1 tutorial
```

## Zero-Knowledge Proofs

Zero-Knowledge Proofs allow one party to prove to another that a statement is true without revealing any information beyond the validity of the statement.

### ZoKrates

ZoKrates is a toolbox for zkSNARKs on Ethereum.

```bash
# Using ZoKrates
zokrates compile -i root.zok
zokrates setup
zokrates compute-witness -a 337 113569
zokrates generate-proof
zokrates verify
```

Example ZoKrates program (root.zok):

```
def main(private field a, field b) -> bool:
    return a * a == b
```

Example workflow:

```bash
# Create a ZoKrates program
cat > root.zok << EOF
def main(private field a, field b) -> bool:
    return a * a == b
EOF

# Compile the program
zokrates compile -i root.zok

# Generate proving and verification keys
zokrates setup

# Compute witness for a = 337, b = 113569 (337^2 = 113569)
zokrates compute-witness -a 337 113569

# Generate proof
zokrates generate-proof

# Verify proof
zokrates verify

# Export verifier as Solidity contract
zokrates export-verifier
```

### Circom and SnarkJS

Circom is a language for writing arithmetic circuits and SnarkJS is a JavaScript implementation of zkSNARKs.

```bash
# Install Circom and SnarkJS
npm install -g circom snarkjs

# Create a Circom circuit
cat > circuit.circom << EOF
pragma circom 2.0.0;

template Multiplier() {
    signal input a;
    signal input b;
    signal output c;
    
    c <== a * b;
}

component main = Multiplier();
EOF

# Compile the circuit
circom circuit.circom --r1cs --wasm --sym

# Generate proving and verification keys
snarkjs plonk setup circuit.r1cs circuit_final.zkey

# Generate witness
node circuit_js/generate_witness.js circuit_js/circuit.wasm input.json witness.wtns

# Generate proof
snarkjs plonk prove circuit_final.zkey witness.wtns proof.json public.json

# Verify proof
snarkjs plonk verify verification_key.json public.json proof.json

# Export verifier as Solidity contract
snarkjs zkey export solidityverifier circuit_final.zkey verifier.sol
```

## Differential Privacy

Differential Privacy is a system for publicly sharing information about a dataset by describing patterns of groups within the dataset while withholding information about individuals.

### Google's Differential Privacy Library

```python
# Python example with Google's Differential Privacy library
from pydp.algorithms.laplacian import BoundedSum, BoundedMean
import numpy as np

# Create a dataset
data = np.random.randint(0, 100, size=1000)

# Set privacy parameters
epsilon = 1.0  # Privacy budget

# Compute differentially private sum
dp_sum = BoundedSum(
    epsilon=epsilon,
    lower_bound=0,
    upper_bound=100
)
result_sum = dp_sum.quick_result(data)
print(f"Original sum: {sum(data)}")
print(f"Differentially private sum: {result_sum}")

# Compute differentially private mean
dp_mean = BoundedMean(
    epsilon=epsilon,
    lower_bound=0,
    upper_bound=100
)
result_mean = dp_mean.quick_result(data)
print(f"Original mean: {np.mean(data)}")
print(f"Differentially private mean: {result_mean}")
```

### IBM's Differential Privacy Library

```python
# Python example with IBM's Differential Privacy library
import numpy as np
from diffprivlib.mechanisms import Laplace
from diffprivlib.models import GaussianNB

# Create a dataset
X = np.random.random((100, 5))
y = np.random.randint(0, 2, size=100)

# Set privacy parameters
epsilon = 1.0  # Privacy budget

# Add noise to a value using Laplace mechanism
mech = Laplace(epsilon=epsilon, sensitivity=1.0)
noisy_value = mech.randomise(42)
print(f"Original value: 42")
print(f"Noisy value: {noisy_value}")

# Train a differentially private classifier
clf = GaussianNB(epsilon=epsilon)
clf.fit(X, y)

# Make predictions
predictions = clf.predict(X[:10])
print(f"Predictions: {predictions}")
```

## Merkle Trees

Merkle Trees are a fundamental data structure in blockchain technology, allowing efficient and secure verification of content in large data structures.

```javascript
// JavaScript example with merkletreejs
const { MerkleTree } = require('merkletreejs');
const SHA256 = require('crypto-js/sha256');

// Create leaf nodes from data
const leaves = ['a', 'b', 'c', 'd'].map(x => SHA256(x));

// Create Merkle tree
const tree = new MerkleTree(leaves, SHA256);

// Get root
const root = tree.getRoot().toString('hex');
console.log('Merkle Root:', root);

// Create proof for leaf 'a'
const leaf = SHA256('a');
const proof = tree.getProof(leaf);
console.log('Proof for a:', proof);

// Verify proof
console.log('Verification result:', tree.verify(proof, leaf, root)); // true

// Create proof for non-existent leaf 'x'
const badLeaf = SHA256('x');
const badProof = tree.getProof(badLeaf);
console.log('Verification result for bad leaf:', tree.verify(badProof, badLeaf, root)); // false
```

## Secure Multi-Party Computation Frameworks

### PySyft

PySyft is a library for secure and private deep learning.

```python
# Python example with PySyft
import syft as sy
import torch

# Initialize PySyft
hook = sy.TorchHook(torch)

# Create virtual workers
alice = sy.VirtualWorker(hook, id="alice")
bob = sy.VirtualWorker(hook, id="bob")

# Create private data for each worker
data_alice = torch.tensor([1, 2, 3, 4]).send(alice)
data_bob = torch.tensor([5, 6, 7, 8]).send(bob)

# Perform secure computation
result = data_alice + data_bob

# Get result
print(result.get())  # tensor([6, 8, 10, 12])
```

### Crypten

Crypten is a framework for secure machine learning.

```python
# Python example with Crypten
import crypten
import torch

# Initialize Crypten
crypten.init()

# Create private tensors
x = torch.tensor([1.0, 2.0, 3.0, 4.0])
y = torch.tensor([5.0, 6.0, 7.0, 8.0])

# Encrypt tensors
x_enc = crypten.cryptensor(x)
y_enc = crypten.cryptensor(y)

# Perform secure computation
sum_enc = x_enc + y_enc
product_enc = x_enc * y_enc

# Decrypt results
sum_dec = sum_enc.get_plain_text()
product_dec = product_enc.get_plain_text()

print("Sum:", sum_dec)  # tensor([6., 8., 10., 12.])
print("Product:", product_dec)  # tensor([5., 12., 21., 32.])
```

## Practical Applications

### Secure Voting System

```javascript
// Example: Secure voting system using homomorphic encryption
const { ElGamal } = require('crypto-lib');  // Hypothetical library

// Generate key pair
const { publicKey, privateKey } = ElGamal.generateKeyPair();

// Encrypt votes (1 for yes, 0 for no)
const encryptVote = (vote, publicKey) => {
  return ElGamal.encrypt(vote, publicKey);
};

// Homomorphically add encrypted votes
const addEncryptedVotes = (encryptedVotes) => {
  return encryptedVotes.reduce((sum, vote) => ElGamal.add(sum, vote));
};

// Decrypt final tally
const decryptTally = (encryptedTally, privateKey) => {
  return ElGamal.decrypt(encryptedTally, privateKey);
};

// Example usage
const votes = [1, 0, 1, 1, 0, 1, 1, 0, 1, 1];  // 7 yes, 3 no
const encryptedVotes = votes.map(vote => encryptVote(vote, publicKey));
const encryptedTally = addEncryptedVotes(encryptedVotes);
const tally = decryptTally(encryptedTally, privateKey);

console.log(`Tally: ${tally} yes votes out of ${votes.length} total votes`);
```

### Private Set Intersection

```python
# Example: Private set intersection using secure multi-party computation
import numpy as np
from crypten.mpc import MPCTensor
import crypten

# Initialize Crypten
crypten.init()

# Alice's set
alice_set = np.array([1, 3, 5, 7, 9])

# Bob's set
bob_set = np.array([2, 3, 5, 8, 9])

# Convert to one-hot encoding
max_val = 10
alice_one_hot = np.zeros(max_val)
bob_one_hot = np.zeros(max_val)

alice_one_hot[alice_set] = 1
bob_one_hot[bob_set] = 1

# Encrypt sets
alice_enc = MPCTensor(alice_one_hot)
bob_enc = MPCTensor(bob_one_hot)

# Compute intersection (element-wise multiplication)
intersection_enc = alice_enc * bob_enc

# Decrypt result
intersection_one_hot = intersection_enc.get_plain_text().numpy()

# Convert back to set
intersection = np.where(intersection_one_hot == 1)[0]

print("Alice's set:", alice_set)
print("Bob's set:", bob_set)
print("Intersection:", intersection)  # Should be [3, 5, 9]
```

### Zero-Knowledge Identity Verification

```javascript
// Example: Zero-knowledge identity verification using zkSNARKs
const snarkjs = require('snarkjs');
const fs = require('fs');

async function verifyIdentity(publicData, privateData) {
  // Load circuit
  const circuit = JSON.parse(fs.readFileSync('identity_circuit.json'));
  
  // Create witness
  const witness = {
    publicData,
    privateData
  };
  
  // Generate proof
  const { proof, publicSignals } = await snarkjs.groth16.fullProve(
    witness,
    'identity_circuit.wasm',
    'identity_proving_key.zkey'
  );
  
  // Verify proof
  const isValid = await snarkjs.groth16.verify(
    'identity_verification_key.json',
    publicSignals,
    proof
  );
  
  return {
    isValid,
    publicSignals
  };
}

// Example usage
async function main() {
  const result = await verifyIdentity(
    { userId: '12345' },  // Public data
    { password: 'secret', biometricHash: '0xabcdef' }  // Private data
  );
  
  console.log(`Identity verification ${result.isValid ? 'successful' : 'failed'}`);
  console.log('Public signals:', result.publicSignals);
}

main().catch(console.error);