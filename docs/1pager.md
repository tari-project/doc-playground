Good day. Please find below for your consideration. Two items I wish to clarify:
* There is no Indexer node for L1; interactions are handled through a combination of the Minotari Base Node and the wallet.
* How one would call prices is not covered at this stage as we have no source for prices.

If anyone would like to add any comments/recommendations, please feel free to do so. My thanks to @stringhandler and @blackwolfsa for their assistance clarifying these points and distinguishing between the RPC and gRPC resources I had included in the same doc. Any errors that remain are my own.

# Summary of Relevant Resources & Documentation

## Tari Integration Guide
The following guide covers both the setup of the minimal required components (The base node and the wallet), the configuration of the base node to allow for RPC calls, and a couple of examples of how one would listen for transactions and check balances. Please note that this is focused on setting up a wallet in a read-only configuration.

[Exchange Integration Guide](https://tari.com/lessons/09_adding_tari_to_your_exchange)

## gRPC Implementation

While comprehensive API documentation currently does not exist for the gRPC methods within Minotari, the relevant parts of the project code for components of the gRPC implementation can be found below, along with some examples of gRPC implementation.

You can find the methods for the gRPC implementation in the following files within the tari-project/tari repository:

### Proto files
* [applications/minotari_app_grpc/proto/base_node.proto](https://github.com/tari-project/tari/blob/f5365caed6b0e03559158c1556d1cf7f0b5e794a/applications/minotari_app_grpc/proto/base_node.proto): Defines the gRPC service BaseNode with methods like ListHeaders, GetBlocks, SubmitTransaction, GetNewBlockTemplate, and more.
* [applications/minotari_app_grpc/proto/wallet.proto](https://github.com/tari-project/tari/blob/f5365caed6b0e03559158c1556d1cf7f0b5e794a/applications/minotari_app_grpc/proto/wallet.proto): Defines the gRPC interface for interacting with the wallet, including operations for getting version, state, balance, transferring funds, and managing transactions.

### Server-side implementation
* [applications/minotari_node/src/grpc/base_node_grpc_server.rs](https://github.com/tari-project/tari/blob/f5365caed6b0e03559158c1556d1cf7f0b5e794a/applications/minotari_node/src/grpc/base_node_grpc_server.rs): Implements the gRPC server for the base node, including methods like get_mempool_stats, get_shard_key, get_active_validator_nodes, and others.

### Transactions
* [transaction_components/mod.rs](https://github.com/tari-project/tari/blob/f5365caed6b0e03559158c1556d1cf7f0b5e794a/base_layer/core/src/transactions/transaction_components/mod.rs)
* [transaction_components/transaction_builder.rs](https://github.com/tari-project/tari/blob/f5365caed6b0e03559158c1556d1cf7f0b5e794a/base_layer/core/src/transactions/transaction_components/transaction_builder.rs)
* [wallet_output_builder.rs](https://github.com/tari-project/tari/blob/f5365caed6b0e03559158c1556d1cf7f0b5e794a/base_layer/core/src/transactions/transaction_components/wallet_output_builder.rs)

### Additional resources
* [base_layer/core/src/base_node/rpc/mod.rs](https://github.com/tari-project/tari/blob/f5365caed6b0e03559158c1556d1cf7f0b5e794a/base_layer/core/src/base_node/rpc/mod.rs): Contains the trait BaseNodeWalletService which defines various RPC methods such as submit_transaction, transaction_query, fetch_matching_utxos, get_tip_info, and more.
* [service.rs](https://github.com/tari-project/tari/blob/f5365caed6b0e03559158c1556d1cf7f0b5e794a/base_layer/core/src/base_node/rpc/service.rs#L170-L272): This service handles RPC (Remote Procedure Call) interactions for submitting transactions, querying transactions, fetching UTXOs, and other blockchain-related operations.
* [comms/dht/src/rpc/mod.rs](https://github.com/tari-project/tari/blob/f5365caed6b0e03559158c1556d1cf7f0b5e794a/comms/dht/src/rpc/mod.rs): Defines the DHT RPC interface with methods like get_closer_peers and get_peers.

## Instructions and Examples
### Webhook Setup

Below is a typical example of a webhook endpoint in Javascript:

```javascript
const express = require('express');
const app = express();
const port = 5000;

app.use(express.json());

app.post('/webhook', (req, res) => {
    const data = req.body;
    // Process the transaction confirmation data here
    console.log(`Received transaction confirmation: ${JSON.stringify(data)}`);
    res.json({ status: 'success' });
});

app.listen(port, () => {
    console.log(`Server is running on port ${port}`);
});
```

An example of a typical script for providing data for the webhook can be found at [notify_example.sh](https://github.com/tari-project/tari/blob/f5365caed6b0e03559158c1556d1cf7f0b5e794a/applications/minotari_console_wallet/src/notifier/notify_example.sh)

Below is an example of the script, modified to check for transaction confirmations based on their status and utlising the webhook:

```sh
#!/bin/sh
# example notify script

# append the arguments to a log file
echo "$@" >>notify.log

# post to a webhook url
webhook_url="https://your-webhook-url.com"

case $1 in
received)
    ;;
confirmation)
    msg="Transaction confirmed: \nAmount: **$2** \nMessage: **$4** \nTx ID: ${3} \nConfirmations: ${12}"
    curl -i -X POST -H 'Content-Type: application/json' -d '{"text": "'"${msg}"'"}' $webhook_url
    ;;
mined)
    ;;
sent)
    ;;
cancelled)
    ;;
esac
```
* When a `confirmation` event occurs, it constructs a message (`msg`) with the transaction details.
* It then sends this message to the webhook URL using `curl`.

You can further customize the script to handle other events or add additional logic as needed. This setup allows you to monitor transaction confirmations and receive notifications whenever a transaction is confirmed.

### Submitting a transaction and checking it status

#### Setup gRPC Client
Ensure you have a gRPC client setup in your preferred programming language (e.g., Python). You'll need to import the `base_node.proto` file to generate the client code.

#### Submit a Transaction
Use the `SubmitTransaction` RPC method to send the transaction to the base node.

Example in Python:
```python
   import grpc
   import base_node_pb2
   import base_node_pb2_grpc

   def submit_transaction():
       # Establish a connection to the gRPC server
       channel = grpc.insecure_channel('localhost:18142')
       stub = base_node_pb2_grpc.BaseNodeStub(channel)

       # Construct a Transaction message with real values
       transaction = base_node_pb2.Transaction(
           offset=b'...',
           body=base_node_pb2.AggregateBody(
               inputs=[base_node_pb2.TransactionInput(
                   commitment=b'...', # Example commitment
                   script_sig=b'...', # Example script signature
                   features=base_node_pb2.OutputFeatures(
                       flags=0, # Example flag
                       maturity=0 # Example maturity
                   )
               )],
               outputs=[base_node_pb2.TransactionOutput(
                   commitment=b'...', # Example commitment
                   features=base_node_pb2.OutputFeatures(
                       flags=0, # Example flag
                       maturity=0 # Example maturity
                   ),
                   proof=b'...' # Example proof
               )],
               kernels=[base_node_pb2.TransactionKernel(
                   features=0, # Example feature
                   fee=1000, # Example fee
                   lock_height=0, # Example lock height
                   excess=b'...', # Example excess
                   excess_sig=base_node_pb2.Signature(
                       public_nonce=b'...', # Example public nonce
                       signature=b'...' # Example signature
                   )
               )]
           ),
           script_offset=b'...' # Example script offset
       )

       # Submit the transaction
       response = stub.SubmitTransaction(base_node_pb2.SubmitTransactionRequest(transaction=transaction))
       print(f"Transaction submission result: {response.result}")

   if __name__ == "__main__":
       submit_transaction()
```

#### Check Transaction Status:
Use the `TransactionState` RPC method to check the status of the transaction.

Example in Python:

```python
   import grpc
   import base_node_pb2
   import base_node_pb2_grpc

   def check_transaction_status():
       # Establish a connection to the gRPC server
       channel = grpc.insecure_channel('localhost:18142')
       stub = base_node_pb2_grpc.BaseNodeStub(channel)

       # Check the transaction state using the transaction's excess signature
       excess_sig = base_node_pb2.Signature(
           public_nonce=b'...', # Example public nonce used in the transaction
           signature=b'...' # Example signature used in the transaction
       )

       response = stub.TransactionState(base_node_pb2.TransactionStateRequest(excess_sig=excess_sig))
       print(f"Transaction state: {response.result}")

   if __name__ == "__main__":
       check_transaction_status()
```

#### Comments on the Code
**Establishing Connection:**
The `grpc.insecure_channel('localhost:18142')` creates a channel to the gRPC server running on `localhost` at port `18142`.

**Constructing Transaction:**
The `Transaction` message includes various fields such as `inputs`, `outputs`, and `kernels`. Each field must be populated with appropriate values.

**Submitting Transaction:**
The `SubmitTransaction` method sends the constructed transaction to the base node. The response provides the result of the submission.

**Checking Status:**
The `TransactionState` method checks the status of the transaction using its `excess_sig`. The response indicates whether the transaction is in the mempool, mined, or not stored.

You can find the proto file definition [here](https://github.com/tari-project/tari/blob/f5365caed6b0e03559158c1556d1cf7f0b5e794a/applications/minotari_app_grpc/proto/base_node.proto) for more details.


## Wallet
The ```minotari_console_wallet``` binary is available for most transaction-based actions, and provides a gRPC interface for doing so. You can read more about the ```minotari_console_wallet``` here: [Wallet Readme.md](https://github.com/tari-project/tari/blob/development/applications/minotari_console_wallet/README.md)

## Applicable RFC documents
### Base Node
* [RFC-0111: Base Node Architecture](https://rfc.tari.com/RFC-0111_BaseNodeArchitecture.html)
* [RFC-0110: Base Nodes](https://rfc.tari.com/RFC-0110_BaseNodes.html)

### Transactions
* [RFC-0201: TariScript](https://rfc.tari.com/RFC-0201_TariScript.html)
* [RFC-0250: Covenants](https://rfc.tari.com/RFC-0250_Covenants.html)
* [RFC-0230: Time related transactions](https://rfc.tari.com/RFC-0230_HTLC.html)
* [RFC-0240: Atomic swaps](https://rfc.tari.com/RFC-0240_AtomicSwap.html)
* [RFC-0310: Submarine Swaps](https://rfc.tari.com/RFC-0310_SubmarineSwaps.html)
* [RFC-0202: TariScript Opcodes](https://rfc.tari.com/RFC-0202_TariScriptOpcodes.html)
* [RFC-0203: Stealth Addresses](https://rfc.tari.com/RFC-0203_StealthAddresses.html)

### Github Resources
* [Tari](https://github.com/tari-project/tari/)
* [Minotari Console Wallet Readme](https://github.com/tari-project/tari/blob/development/applications/minotari_console_wallet/README.md)