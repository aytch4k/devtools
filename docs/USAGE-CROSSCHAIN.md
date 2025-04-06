# Cross-Chain Development

This section covers development of cross-chain applications and protocols.

## Table of Contents

- [Introduction](./USAGE-INTRO.md)
- [Blockchain Development](./USAGE-BLOCKCHAIN.md)
- [Layer 2 Development](./USAGE-LAYER2.md)
- [Cross-Chain Development](#cross-chain-development) (this file)
- [Smart Wallet Integration](./USAGE-WALLETS.md)
- [Frontend Development](./USAGE-FRONTEND.md)
- [Decentralized Storage](./USAGE-STORAGE.md)
- [Database Systems](./USAGE-DATABASES.md)
- [Advanced Cryptography](./USAGE-CRYPTO.md)
- [WebXR/AR/VR Development](./USAGE-WEBXR.md)
- [CI/CD Integration](./USAGE-CICD.md)

## IBC (Inter-Blockchain Communication)

IBC is the protocol used by Cosmos chains for cross-chain communication.

```bash
# Set up two Cosmos chains
cd /app/src
ignite scaffold chain chain-a
ignite scaffold chain chain-b

# Add IBC module to both chains
cd chain-a
ignite scaffold module ibc --ibc
cd ../chain-b
ignite scaffold module ibc --ibc

# Configure the relayer
rly config init
rly chains add -f chain-a.json
rly chains add -f chain-b.json
rly paths add chain-a chain-b path-ab

# Start the chains
cd ../chain-a
ignite chain serve &
cd ../chain-b
ignite chain serve &

# Create and link relayer accounts
rly keys restore chain-a testkey "mnemonic words here"
rly keys restore chain-b testkey "mnemonic words here"
rly tx link path-ab

# Start the relayer
rly start path-ab
```

Example IBC module:

```go
// x/mymodule/types/packet.go
package types

import (
    sdk "github.com/cosmos/cosmos-sdk/types"
    sdkerrors "github.com/cosmos/cosmos-sdk/types/errors"
)

// IBC packet data structure
type MyPacketData struct {
    Sender    string
    Content   string
    Timestamp uint64
}

// ValidateBasic does a simple validation check
func (p MyPacketData) ValidateBasic() error {
    if p.Sender == "" {
        return sdkerrors.Wrap(sdkerrors.ErrInvalidAddress, "sender cannot be empty")
    }
    if p.Content == "" {
        return sdkerrors.Wrap(sdkerrors.ErrInvalidRequest, "content cannot be empty")
    }
    return nil
}

// GetBytes encodes the packet data into bytes
func (p MyPacketData) GetBytes() []byte {
    return sdk.MustSortJSON(ModuleCdc.MustMarshalJSON(&p))
}
```

## Polkadot Parachains

Parachains are specialized blockchains that connect to the Polkadot Relay Chain.

```bash
# Create a new parachain using Substrate Parachain Template
git clone https://github.com/substrate-developer-hub/substrate-parachain-template
cd substrate-parachain-template

# Build the parachain
cargo build --release

# Generate a parachain specification
./target/release/parachain-template-node build-spec --disable-default-bootnode > para-spec.json

# Export the genesis state and validation function
./target/release/parachain-template-node export-genesis-state --parachain-id 2000 > para-2000-genesis
./target/release/parachain-template-node export-genesis-wasm > para-2000-wasm

# Register the parachain with the relay chain
polkadot-js-api --ws ws://polkadot-node:9944 tx.parasSudoWrapper.sudoScheduleParaInitialize \
  '{"genesisHead":"0x...","validationCode":"0x...","paraId":2000}'
```

Example XCMP (Cross-Chain Message Passing) pallet:

```rust
#[pallet::pallet]
#[pallet::generate_store(pub(super) trait Store)]
pub struct Pallet<T>(_);

#[pallet::config]
pub trait Config: frame_system::Config {
    type RuntimeEvent: From<Event<Self>> + IsType<<Self as frame_system::Config>::RuntimeEvent>;
    type XcmSender: SendXcm;
}

#[pallet::event]
#[pallet::generate_deposit(pub(super) fn deposit_event)]
pub enum Event<T: Config> {
    XcmMessageSent { destination: MultiLocation, message: Xcm<()> },
}

#[pallet::call]
impl<T: Config> Pallet<T> {
    #[pallet::weight(10_000)]
    pub fn send_xcm_message(
        origin: OriginFor<T>,
        dest: MultiLocation,
        message: Xcm<()>,
    ) -> DispatchResult {
        let who = ensure_signed(origin)?;
        
        T::XcmSender::send_xcm(dest.clone(), message.clone())
            .map_err(|_| Error::<T>::XcmSendFailed)?;
            
        Self::deposit_event(Event::XcmMessageSent { 
            destination: dest,
            message,
        });
        
        Ok(())
    }
}
```

## Wormhole Bridge

Wormhole is a generic message passing protocol connecting multiple blockchains.

```bash
# Initialize a Wormhole project
mkdir wormhole-project && cd wormhole-project
npm init -y
npm install @wormhole-foundation/sdk

# Create a cross-chain token transfer script
cat > transfer.js << EOF
const { WormholeSDK } = require('@wormhole-foundation/sdk');

async function transferToken() {
  // Initialize Wormhole SDK
  const sdk = await WormholeSDK.for(
    ['Ethereum', 'Solana'],
    {
      Ethereum: { rpc: 'http://geth:8545' },
      Solana: { rpc: 'http://solana:8899' }
    }
  );

  // Transfer tokens from Ethereum to Solana
  const receipt = await sdk.tokenBridge.transfer({
    from: {
      chain: 'Ethereum',
      address: '0xYourEthereumAddress',
      token: '0xTokenAddress',
      amount: '1000000000000000000' // 1 token with 18 decimals
    },
    to: {
      chain: 'Solana',
      address: 'YourSolanaAddress'
    }
  });

  console.log('Transfer complete:', receipt);
}

transferToken().catch(console.error);
EOF

# Run the transfer script
node transfer.js
```

Example Wormhole contract (Solidity):

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@wormhole-foundation/wormhole-solidity-sdk/WormholeRelayerSDK.sol";

contract CrossChainMessenger is TokenSender, TokenReceiver {
    string public lastReceivedMessage;
    
    constructor(address _wormholeRelayer, address _tokenBridge, address _wormhole) 
        TokenBase(_wormholeRelayer, _tokenBridge, _wormhole) {}
    
    function sendMessage(
        uint16 targetChain,
        address targetAddress,
        string memory message
    ) public payable {
        bytes memory payload = abi.encode(message);
        
        sendTokenWithPayloadToEvm(
            targetChain,
            targetAddress,
            payload,
            0, // value in wei
            0  // gas limit
        );
    }
    
    function receivePayload(
        bytes memory payload,
        TokenReceived[] memory,
        bytes32,
        uint16,
        bytes32
    ) internal override {
        lastReceivedMessage = abi.decode(payload, (string));
    }
}
```

## Axelar Cross-Chain Gateway

Axelar provides secure cross-chain communication.

```bash
# Initialize an Axelar project
mkdir axelar-project && cd axelar-project
npm init -y
npm install @axelar-network/axelarjs-sdk

# Create a cross-chain message passing script
cat > send-message.js << EOF
const { AxelarQueryAPI, Environment, EvmChain, GasToken } = require('@axelar-network/axelarjs-sdk');

async function sendMessage() {
  const axelarQuery = new AxelarQueryAPI({
    environment: Environment.DEVNET,
  });

  // Get gas estimate for sending a message from Ethereum to Avalanche
  const gasFee = await axelarQuery.estimateGasFee(
    EvmChain.ETHEREUM,
    EvmChain.AVALANCHE,
    GasToken.ETH,
    700000,
    2
  );

  console.log('Estimated gas fee:', gasFee);
  
  // In a real application, you would use the Axelar Gateway contract to send the message
  // const gateway = new ethers.Contract(gatewayAddress, gatewayABI, signer);
  // const tx = await gateway.sendMessage(
  //   destinationChain,
  //   destinationAddress,
  //   message,
  //   { value: gasFee }
  // );
}

sendMessage().catch(console.error);
EOF

# Run the script
node send-message.js
```

Example Axelar contract (Solidity):

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import { AxelarExecutable } from '@axelar-network/axelar-gmp-sdk-solidity/contracts/executable/AxelarExecutable.sol';
import { IAxelarGateway } from '@axelar-network/axelar-gmp-sdk-solidity/contracts/interfaces/IAxelarGateway.sol';
import { IAxelarGasService } from '@axelar-network/axelar-gmp-sdk-solidity/contracts/interfaces/IAxelarGasService.sol';

contract CrossChainCounter is AxelarExecutable {
    IAxelarGasService public immutable gasService;
    
    uint256 public count;
    string public lastSourceChain;
    string public lastSourceAddress;
    
    event CountIncremented(uint256 newCount, string sourceChain, string sourceAddress);
    
    constructor(address gateway_, address gasService_) AxelarExecutable(gateway_) {
        gasService = IAxelarGasService(gasService_);
    }
    
    function incrementRemote(
        string calldata destinationChain,
        string calldata destinationAddress,
        uint256 incrementBy
    ) external payable {
        bytes memory payload = abi.encode(incrementBy);
        
        gasService.payNativeGasForContractCall{value: msg.value}(
            address(this),
            destinationChain,
            destinationAddress,
            payload,
            msg.sender
        );
        
        gateway.callContract(destinationChain, destinationAddress, payload);
    }
    
    function _execute(
        string calldata sourceChain,
        string calldata sourceAddress,
        bytes calldata payload
    ) internal override {
        uint256 incrementBy = abi.decode(payload, (uint256));
        
        count += incrementBy;
        lastSourceChain = sourceChain;
        lastSourceAddress = sourceAddress;
        
        emit CountIncremented(count, sourceChain, sourceAddress);
    }
}
```

## LayerZero

LayerZero is an omnichain interoperability protocol.

```bash
# Initialize a LayerZero project
mkdir layerzero-project && cd layerzero-project
npm init -y
npm install @layerzerolabs/lz-sdk

# Create a script to interact with LayerZero
cat > index.js << EOF
const { ChainId } = require('@layerzerolabs/lz-sdk');

// Chain IDs in LayerZero
console.log('Ethereum Chain ID:', ChainId.ETHEREUM);
console.log('Avalanche Chain ID:', ChainId.AVALANCHE);
console.log('Polygon Chain ID:', ChainId.POLYGON);
EOF

# Run the script
node index.js
```

Example LayerZero contract (Solidity):

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@layerzerolabs/solidity-examples/contracts/lzApp/NonblockingLzApp.sol";

contract CrossChainMessage is NonblockingLzApp {
    string public message;
    
    event MessageReceived(string message, uint16 srcChainId);
    
    constructor(address _endpoint) NonblockingLzApp(_endpoint) {}
    
    function sendMessage(
        uint16 _dstChainId,
        bytes calldata _dstAddress,
        string calldata _message
    ) public payable {
        bytes memory payload = abi.encode(_message);
        _lzSend(_dstChainId, payload, payable(msg.sender), address(0x0), bytes(""), msg.value);
    }
    
    function _nonblockingLzReceive(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) internal override {
        message = abi.decode(_payload, (string));
        emit MessageReceived(message, _srcChainId);
    }
}
```

## Chainlink CCIP (Cross-Chain Interoperability Protocol)

Chainlink CCIP provides a secure cross-chain messaging solution.

```bash
# Initialize a CCIP project
mkdir ccip-project && cd ccip-project
npm init -y
npm install @chainlink/contracts

# Create a CCIP sender contract
cat > CCIPSender.sol << EOF
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import {IRouterClient} from "@chainlink/contracts-ccip/src/v0.8/ccip/interfaces/IRouterClient.sol";
import {Client} from "@chainlink/contracts-ccip/src/v0.8/ccip/libraries/Client.sol";
import {LinkTokenInterface} from "@chainlink/contracts/src/v0.8/interfaces/LinkTokenInterface.sol";

contract CCIPSender {
    IRouterClient router;
    LinkTokenInterface linkToken;

    constructor(address _router, address _link) {
        router = IRouterClient(_router);
        linkToken = LinkTokenInterface(_link);
    }

    function sendMessage(
        uint64 destinationChainSelector,
        address receiver,
        string memory message
    ) external {
        Client.EVM2AnyMessage memory evm2AnyMessage = Client.EVM2AnyMessage({
            receiver: abi.encode(receiver),
            data: abi.encode(message),
            tokenAmounts: new Client.EVMTokenAmount[](0),
            extraArgs: "",
            feeToken: address(linkToken)
        });

        uint256 fees = router.getFee(destinationChainSelector, evm2AnyMessage);
        
        linkToken.approve(address(router), fees);
        
        bytes32 messageId = router.ccipSend(destinationChainSelector, evm2AnyMessage);
        
        return messageId;
    }
}
EOF
```

## Cosmos App-Chains

Cosmos allows developers to create application-specific blockchains.

```bash
# Create a new Cosmos app-chain
cd /app/src
ignite scaffold chain myappchain

# Add modules to the chain
cd myappchain
ignite scaffold module mymodule

# Add a message type
ignite scaffold message createPost title content

# Add a query
ignite scaffold query posts

# Start the chain
ignite chain serve
```

## Avalanche Subnets

Avalanche Subnets are custom, application-specific blockchains.

```bash
# Create a new Avalanche Subnet
avalanche subnet create mySubnet

# Add a custom VM
avalanche subnet addChain mySubnet \
  --vm SubnetEVM \
  --chain-name myEVMChain

# Deploy the Subnet locally
avalanche subnet deploy mySubnet --local