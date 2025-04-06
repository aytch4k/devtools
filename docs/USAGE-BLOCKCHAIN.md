# Blockchain Development

This section covers development on various blockchain platforms.

## Table of Contents

- [Introduction](./USAGE-INTRO.md)
- [Blockchain Development](#blockchain-development) (this file)
- [Layer 2 Development](./USAGE-LAYER2.md)
- [Cross-Chain Development](./USAGE-CROSSCHAIN.md)
- [Smart Wallet Integration](./USAGE-WALLETS.md)
- [Frontend Development](./USAGE-FRONTEND.md)
- [Decentralized Storage](./USAGE-STORAGE.md)
- [Database Systems](./USAGE-DATABASES.md)
- [Advanced Cryptography](./USAGE-CRYPTO.md)
- [WebXR/AR/VR Development](./USAGE-WEBXR.md)
- [CI/CD Integration](./USAGE-CICD.md)

## Ethereum (and EVM-compatible chains)

```bash
# Create a new Hardhat project
mkdir my-eth-project && cd my-eth-project
npx hardhat init

# Configure to use local node
# Edit hardhat.config.js to include:
networks: {
  localhost: {
    url: "http://geth:8545",
  },
}

# Compile contracts
npx hardhat compile

# Run tests
npx hardhat test

# Deploy to local node
npx hardhat run scripts/deploy.js --network localhost

# Interact with contracts using ethers.js
npm install ethers
```

Example contract:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SimpleStorage {
    uint256 private value;
    
    event ValueChanged(uint256 newValue);
    
    function setValue(uint256 _value) public {
        value = _value;
        emit ValueChanged(_value);
    }
    
    function getValue() public view returns (uint256) {
        return value;
    }
}
```

## Solana

```bash
# Create a new Solana program
cargo new --lib mysolanaprogram

# Configure for Solana
cd mysolanaprogram
# Add to Cargo.toml:
[dependencies]
solana-program = "1.18.4"
[lib]
crate-type = ["cdylib", "lib"]

# Build the program
cargo build-bpf

# Deploy to local validator
solana config set --url http://solana:8899
solana program deploy target/deploy/mysolanaprogram.so
```

Example Solana program:

```rust
use solana_program::{
    account_info::AccountInfo,
    entrypoint,
    entrypoint::ProgramResult,
    msg,
    pubkey::Pubkey,
};

// Declare the program entrypoint
entrypoint!(process_instruction);

// Program entrypoint implementation
pub fn process_instruction(
    program_id: &Pubkey,
    accounts: &[AccountInfo],
    instruction_data: &[u8],
) -> ProgramResult {
    msg!("Hello, Solana!");
    Ok(())
}
```

## Cosmos SDK

```bash
# Create a new Cosmos SDK chain
cd /app/src
ignite scaffold chain mycosmos

# Add IBC module
cd mycosmos
ignite scaffold module ibc --ibc

# Start the chain
ignite chain serve

# Configure relayer for IBC
rly config init
rly chains add -f cosmos_config.json
```

Example module:

```go
// x/mymodule/keeper/msg_server.go
package keeper

import (
    "context"
    
    sdk "github.com/cosmos/cosmos-sdk/types"
    "mycosmos/x/mymodule/types"
)

type msgServer struct {
    Keeper
}

// NewMsgServerImpl returns an implementation of the MsgServer interface
func NewMsgServerImpl(keeper Keeper) types.MsgServer {
    return &msgServer{Keeper: keeper}
}

// CreatePost creates a new post
func (k msgServer) CreatePost(goCtx context.Context, msg *types.MsgCreatePost) (*types.MsgCreatePostResponse, error) {
    ctx := sdk.UnwrapSDKContext(goCtx)
    
    // Create post logic here
    
    return &types.MsgCreatePostResponse{}, nil
}
```

## Polkadot/Substrate

```bash
# Create a new Substrate node
git clone https://github.com/substrate-developer-hub/substrate-node-template
cd substrate-node-template
cargo build --release

# Create a new Substrate frontend
git clone https://github.com/substrate-developer-hub/substrate-front-end-template
cd substrate-front-end-template
yarn install
yarn start
```

Example pallet:

```rust
#[pallet::pallet]
#[pallet::generate_store(pub(super) trait Store)]
pub struct Pallet<T>(_);

#[pallet::config]
pub trait Config: frame_system::Config {
    type RuntimeEvent: From<Event<Self>> + IsType<<Self as frame_system::Config>::RuntimeEvent>;
}

#[pallet::storage]
#[pallet::getter(fn something)]
pub type Something<T> = StorageValue<_, u32>;

#[pallet::event]
#[pallet::generate_deposit(pub(super) fn deposit_event)]
pub enum Event<T: Config> {
    SomethingStored { something: u32, who: T::AccountId },
}

#[pallet::call]
impl<T: Config> Pallet<T> {
    #[pallet::weight(10_000)]
    pub fn do_something(origin: OriginFor<T>, something: u32) -> DispatchResult {
        let who = ensure_signed(origin)?;
        <Something<T>>::put(something);
        Self::deposit_event(Event::SomethingStored { something, who });
        Ok(())
    }
}
```

## Near Protocol

```bash
# Initialize Near project
near login
near dev-deploy

# Create a new project
npx create-near-app my-near-app
cd my-near-app
npm install
npm run dev
```

Example contract:

```javascript
// assembly/index.ts
import { storage, Context } from "near-sdk-as";

export function setGreeting(message: string): void {
  storage.set("message", message);
}

export function getGreeting(): string {
  return storage.get<string>("message") || "Hello";
}
```

## Cardano

```bash
# Set up a Cardano development environment
cd /app/src
mkdir cardano-project && cd cardano-project

# Create a simple Plutus script
cat > validator.hs << EOF
{-# LANGUAGE DataKinds           #-}
{-# LANGUAGE NoImplicitPrelude   #-}
{-# LANGUAGE ScopedTypeVariables #-}
{-# LANGUAGE TemplateHaskell     #-}
{-# LANGUAGE TypeApplications    #-}
{-# LANGUAGE TypeFamilies        #-}

module Validator where

import           PlutusTx.Prelude
import qualified PlutusTx
import           Plutus.V1.Ledger.Scripts
import           Plutus.V1.Ledger.Api

-- Simple validator that always succeeds
validator :: () -> () -> ScriptContext -> Bool
validator _ _ _ = True

program :: Script
program = unValidatorScript $$(PlutusTx.compile [|| validator ||])

EOF

# Compile the Plutus script
cabal build
```

## Tezos

```bash
# Set up a Tezos development environment
cd /app/src
mkdir tezos-project && cd tezos-project

# Create a simple contract
cat > contract.tz << EOF
parameter int;
storage int;
code { CAR;
       PUSH int 1;
       ADD;
       NIL operation;
       PAIR }
EOF

# Deploy the contract to local node
tezos-client originate contract increment transferring 0 from alice running contract.tz --init 0 --burn-cap 0.295
```

## Algorand

```bash
# Set up an Algorand development environment
cd /app/src
mkdir algorand-project && cd algorand-project

# Create a PyTeal contract
cat > contract.py << EOF
from pyteal import *

def approval_program():
    handle_creation = Return(Int(1))
    handle_optin = Return(Int(1))
    handle_closeout = Return(Int(1))
    handle_updateapp = Return(Int(0))
    handle_deleteapp = Return(Int(0))
    
    scratchCount = ScratchVar(TealType.uint64)
    
    add = Seq([
        scratchCount.store(App.globalGet(Bytes("Count"))),
        App.globalPut(Bytes("Count"), scratchCount.load() + Int(1)),
        Return(Int(1))
    ])
    
    handle_noop = Cond(
        [Txn.application_args[0] == Bytes("Add"), add]
    )
    
    program = Cond(
        [Txn.application_id() == Int(0), handle_creation],
        [Txn.on_completion() == OnComplete.OptIn, handle_optin],
        [Txn.on_completion() == OnComplete.CloseOut, handle_closeout],
        [Txn.on_completion() == OnComplete.UpdateApplication, handle_updateapp],
        [Txn.on_completion() == OnComplete.DeleteApplication, handle_deleteapp],
        [Txn.on_completion() == OnComplete.NoOp, handle_noop]
    )
    
    return program

def clear_state_program():
    return Return(Int(1))
    
if __name__ == "__main__":
    with open("approval.teal", "w") as f:
        compiled = compileTeal(approval_program(), Mode.Application, version=5)
        f.write(compiled)
        
    with open("clear.teal", "w") as f:
        compiled = compileTeal(clear_state_program(), Mode.Application, version=5)
        f.write(compiled)
EOF

# Compile the PyTeal contract
python3 contract.py