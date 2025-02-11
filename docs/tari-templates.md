# Getting Started with Template & Ootle Development

Development on the Ootle, at its most basic, begins with the templates and smart contracts generated from them. These are the foundational blocks required to perform any Ootle specific functions.

But what are these? Think of a template as a specification for a smart contract. From a template, a user can create a smart contract for anything from a simple counter system to a fully-fleged NFT series. This contract then exists on its own, independent of the template, and can be used to perform specific actions. 

The community has focused on developing many templates for common activities envisioned for the Ootle with security and stability in mind. Rather than reinvent the wheel every time you would like to, for example, create an NFT series, you can leverage the templates that have been incorporated into the template library. However, for your own unique services or requirements, you may need to create your own.

So, to develop within the Ootle (both apps or Tari templates), you're going to need the following:

* Ideally, you'll need to set up Rust in your desired Integrated Development Environment (IDE). If you're unsure where to start, our [Setting Up Your Development Environment Guide](https://tari.com/lessons/08_setting_up_development_environment) is a good starting point
* You will need a Minotari Base Node, 

* The Tari Swarm is a testing tool that handles the creation and configuration of Indexer Nodes, Validator Nodes and other factors on your local machine, with a useful web interface for testing contracts and performing other actions. 
* [Optional] The Tari CLI tool allows prospective template developers to quickly set up a basic template project select from a number of pre-defined templates to review. Users can also deploy templates using the tool with fee estimation and more.

This Getting Started guide assumes some basic knowledge of Git, Cargo and Rust.

## Basic Concepts

- **Runtime Environment**: The runtime environment handles various elements such as buckets, proofs, substates, and components. It provides methods for manipulating these elements and managing their interactions. This includes the Tari Virtual Machine (TVM)
- **Workspace**: Manages variables and proofs, providing methods to insert and retrieve indexed values. It's a localised instance of the variables required when performing a transaction against a smart contract that exists within the TVM.
- **Component**: A reusable and addressable entity with associated state and methods. This exists as a substate on the Ootle, that can be called when required via a unique address.
- **Bucket**: Handles resources within the component or workspace. Buckets are used to securely move data between vaults.
- **Vault**: Securely stores resources and manages access control. An example of a vault is a collection of tokens, which needs to be updated as people withdraw or deposit more tokens into the vault.

Some examples include:

### **Creating a Component**
Components represent reusable and addressable entities with associated state and methods.
   ```rust
   let component_address = component_manager.create_component(data);
   ```

### **Managing a Bucket**
Buckets handle resources within the component or workspace.
   ```rust
   let bucket_id = working_state.new_bucket_id();
   working_state.new_bucket(bucket_id, resource_container)?;
   ```

### **Handling Proofs**
Proofs are used for authorization and validation within the component.
   ```rust
   let proof_id = ProofId::new();
   working_state.new_proof(proof_id, locked_resource)?;
   ```
### **Creating and Managing Vaults**
Vaults securely store resources and manage access control.
   ```rust
   let resource_container = ResourceContainer::new(resource_address, amount);
   let vault = Vault::new(resource_container);
   workspace.insert_vault(vault_id, vault)?;
   ```
### **Accessing a Vault**
Components or accounts can interact with vaults to deposit or withdraw resources.
   ```rust
   let vault = workspace.get_vault(vault_id)?;
   vault.deposit(amount);
   ```

### **Runtime Environment Interaction**
The runtime environment manages the execution of code and interactions between components. It handles tasks such as logging, memory management, and invoking actions on components.
   ```rust
      // Example of logging within the runtime environment
   runtime.emit_log(LogLevel::Info, "Component created".to_string())?;

   // Example of invoking a method on a component
   let component_header = runtime.load_component(&component_address)?;
   ```

## Setting up the Tari Swarm 

To set up the Tari Swarm, you will need to run it from the official repos.

* [Tari](https://github.com/tari-project/tari)
* [The Ootle](https://github.com/tari-project/tari-dan)

First, open the terminal and create a folder that you would like to clone both repos into, then clone the repo using their **https** link from the Code dropdown. You'll need to run the following commands:

```sh
git clone https://github.com/tari-project/tari.git
git clone https://github.com/tari-project/tari-dan.git
```

Change the directory to the cloned tari directory. You need to use a specific branch (feature-dan2) in order to successfully build the project. Run:

```bash
git checkout feature-dan2
```

Next, change the directory to the tari-dan folder. You will need to create a config file for the Tari Swarm Daemon, so run:

```bash
cargo run --bin tari_swarm_daemon --release -- -c data/swarm/config.toml init
```

This will create the necessary **config.toml** file. Feel free to open the file and confirm the settings. While most of the defaults are fine, you should update the number of validator nodes to at least 2. This will allow your local Tari swarm to be able to form a committee and reach consensus on any instruction that is submitted to the indexer node.

```toml
[[instances]]
name = "Validator node"
instance_type = "TariValidatorNode"
num_instances = 1

[instances.settings]
```

Once you have updated the config.toml to your liking, save the changes. You will then need to run it from the same folder you ran the previous cargo command from. Run:

```bash
cargo run --bin tari_swarm_daemon --release -- -c data/swarm/config.toml start
```

As this loads, you will be presented with the Tari terminal interface, and it will proceed to run through some commands.

!!! Warning
    You may encounter an error during this process regarding the base node. This is because the process was likely unable to locate a "localnet" folder inside the minotari-node-00 folder that was created on launch. To resolve this, simply create a "localnet" folder in minotari-node-00 folder.
    ```
    tari-dan
    └── /data
        └── /swarm
            └── /processes
                └── /minotari_node_00
                    └── /localnet ## Create this folder
    ```


Rerun:
```
cargo run --bin tari_swarm_daemon --release -- -c data/swarm/config.toml start
```

Give it a few minutes to get going. Once you've done so, you should be able to browse to the Tari Swarm Daemon by visiting https://localhost:8080

## Writing Your First Template

We'll go through a simple template that will introduce you to the structure of a template and the core elements that are utilised. Below is the code for a template that will create a simple counter

```rust
use tari_template_lib::prelude::*;

#[template]

mod counter {
    pub struct Counter {
        value: u32,  // Contract state
    }

    impl Counter {
        // Method to create a new contract instance
        pub fn new() -> Self {
            Self { value: 0 }
        }

        // A read-only method
        pub fn value(&self) -> u32 {
            self.value
        }

        // A method that updates the contract state
        pub fn increase(&mut self) {
            self.value += 1;
        }
    }
}
```
The major elements of the template are captured in the first two lines.

```rust
use tari_template_lib::prelude::*;
```

This line imports all of the functions, types, macros and traits grouped within the ```prelude``` module of the Tari template library, ```tari_template_lib```. When you compile this code, you will have access to all the functions necessary to interact with the Tari blockchain through these standardised templates. But how?

This is partly explained by the second line:

```rust
#[template]
```

This a unique attribute macro within the Tari template library. This will allow rust to compile this basic code in such a way to generate the necessary code for interacting with the blockchain. So, for example, the following code:

```rust
    pub struct Counter {
        value: u32,  // Contract state
    }
```

Creates a custom data type that stores an integer value in Rust. But how does one "store" a value on the blockchain? The template macro we've selected interprets this instruction and then builds code around it for blockchain interaction. You're still creating a custom data type that can be modified, but the template macro understands what to add to the struct to store it on the blockchain.

Lastly, we have several methods for interacting with the structure contained within the implementation block:

```rust
    impl Counter {
        // Method to create a new contract instance
        pub fn new() -> Self {
            Self { value: 0 }
        }

        // A read-only method
        pub fn value(&self) -> u32 {
            self.value
        }

        // A method that updates the contract state
        pub fn increase(&mut self) {
            self.value += 1;
        }
    }
```

These are the means that we are going to intereact with the data type we've created in the Counter structure. We can create a new counter value (so we can have multiple Counter values running), a means to check the current value of the counters, and a method to increase any counter's value by 1.

## How are templates deployed to the network




### Simple Setup - Wallet Daemon

To create a wallet, add funds to it, and send a transaction using the tools in the tari-dan repository, follow these steps:

#### Create the Wallet

1. **Run the Wallet Daemon**:
   ```
   cargo run --bin tari_dan_wallet_daemon -- -b .
   ```

2. **Create an Account**:
   Use the Tari Dan Wallet CLI to create an account:
   ```
   cargo run --bin tari_dan_wallet_cli -- accounts create --account-name <USER_ACCOUNT_NAME>
   ```

#### Add Funds to the Wallet

1. **Request Airdrop**:
   Use the Tari Dan Wallet CLI to request an airdrop of test coins:
   ```
   cargo run --bin tari_dan_wallet_cli -- accounts faucet --account-name <USER_ACCOUNT_NAME> --amount <AMOUNT> --fee <FEE_AMOUNT>
   ```

2. **Claim Free Testnet Coins**:
   Alternatively, you can claim free testnet coins using the web UI or JSON RPC:
   ```tsx
   const onClaimFreeCoins = async () => {
     await mutateCreateFeeTestCoins({
       accountName: "TestAccount",
       amount: 1_000_000_000,
       fee: 1000,
     });
   };
   ```

### Send a Transaction

1. **Send Money**:
   Use the Tari Dan Wallet CLI to send a transaction:
   ```rust
   pub async fn handle_send(args: SendArgs, client: &mut WalletDaemonClient) -> Result<(), anyhow::Error> {
       let SendArgs {
           source_account_name,
           amount,
           resource_address,
           destination_public_key,
           common,
       } = args;

       let destination_public_key =
           PublicKey::from_canonical_bytes(&destination_public_key.into_inner()).map_err(anyhow::Error::msg)?;

       let fee = common.max_fee.map(|f| f.try_into()).transpose()?;
       let resp = client
           .accounts_transfer(AccountsTransferRequest {
               account: source_account_name,
               amount: Amount::try_from(amount)?,
               resource_address,
               destination_public_key,
               max_fee: fee,
               proof_from_badge_resource: None,
               dry_run: false,
           })
           .await?;

       println!("Transaction: {}", resp.transaction_id);
       println!("Fee: {} ({} refunded)", resp.fee, resp.fee_refunded);
       println!();
       summarize_finalize_result(&resp.result);

       Ok(())
   }
   ```

2. **Using the Web UI**:
   You can also send money using the web UI:
   ```tsx
   export function SendMoneyDialog(props: SendMoneyDialogProps) {
     const INITIAL_VALUES = {
       publicKey: "",
       outputToConfidential: false,
       inputSelection: "PreferRevealed",
       amount: "",
       fee: "",
       badge: null,
     };
     const isConfidential = props.resource_type === "Confidential";
     const [useBadge, setUseBadge] = useState(false);
     const [disabled, setDisabled] = useState(false);
     const [transferFormState, setTransferFormState] = useState(INITIAL_VALUES);
     const [validity, setValidity] = useState<object>({
       publicKey: false,
       amount: false,
     });
     const [allValid, setAllValid] = useState(false);

     const { accountName, setPopup } = useAccountStore();

     const { data } = useAccountsGetBalances(accountName);
     const badges = data?.balances
       ?.filter((b: BalanceEntry) => b.resource_type === "NonFungible" && b.balance > 0)
       .map((b: BalanceEntry) => b.resource_address) as string[];

     const { mutateAsync: sendIt } = useAccountsTransfer(
       accountName,
       parseInt(transferFormState.amount),
       props.resource_address || XTR2,
       transferFormState.publicKey,
       parseInt(transferFormState.fee),
       props.resource_type === "Confidential",
       !transferFormState.outputToConfidential,
       transferFormState.inputSelection as ConfidentialTransferInputSelection,
       transferFormState.badge,
       false,
     );

     function setFormValue(e: React.ChangeEvent<HTMLInputElement>) {
       setTransferFormState({
         ...transferFormState,
         [e.target.name]: e.target.value,
       });
       if (validity[e.target.name as keyof object] !== undefined) {
         setValidity({
           ...validity,
           [e.target.name]: e.target.validity.valid,
         });
       }
     }

     const onTransfer = async () => {
       if (accountName) {
         setDisabled(true);
         if (!isNaN(parseInt(transferFormState.fee))) {
           sendIt?.()
             .then(() => {
               setPopup({ title: "Transaction submitted", error: false });
             })
             .catch((e) => {
               setPopup({ title: "Transaction failed", error: true, message: e.message });
             })
             .finally(() => {
               setDisabled(false);
             });
         }
       }
     };
   }
   ```

These steps should guide you through creating the wallet, adding funds, and sending transactions using the Tari Dan tools. For more details, refer to the [README](https://github.com/tari-project/tari-dan/blob/development/README.md) file and other relevant documentation in the repository.


### Setting up the Tari CLI tool.

This alone will not do anything on the Ootle. For this to work, you need to compile the Rust program into WebAssembly - a WASM file.

While you can certainly build up a Rust program from scratch and subsequently build it, the Ootle provides another tool for the creation of template projects, in the ```tari-cli``` tool. You can find the project here: https://github.com/tari-project/tari-cli

The tool is in active development, and currently has no released binaries. This means you will need to build the binary from the repo direct.

Clone the repo locally, then build the project using the following commands:

```
git clone https://github.com/tari-project/tari-cli.git
cd tari-cli
cargo build --release
```

Once built, you should find the ```tari``` command in the target folder under ```release```

Type the following command to get started:

```
./tari --help
```

This will display a list of associated commands with the command tool. The three common ones ```create```, ```new``` and ```deploy```

### Creating your Tari Project

Let's go through each of these in order. ```create``` will create a base folder in which to store one or more template projects in the ```templates``` folder. Again, if you want more detailed information regarding the command, you can simply type ```./tari create --help```

Run the following command to create a new project folder and associated files (replace the everything contained in the square brackets, including the brackets themselves, with your project name):

```
./tari create [...yourprojectname...]
```

The most important file to concern yourself with within the project folder is the ```tari.config.toml``` file. Opening it will present the following configuration details:

```toml
# The address of the Wallet Daemon 
[networks.local]
wallet-daemon-jrpc-address = "http://127.0.0.1:12009/"
uploader-endpoint = "http://127.0.0.1:8080/upload_template?register_template=false"

# The address of the Minotari Wallet
[networks.local.wallet-grpc-config]
authentication = "none"
address = "http://127.0.0.1:12003/"
```

These configuration details must be changed to reflect the addresses and ports of the wallet daemon and your Minotari wallet. These currently use the Minotari wallet and wallet daemon set up when setting up your local testing environment via the Tari Swarm Daemon. You can find the ports for updating by visiting your localhost:8080 (the default address for the Tari Swarm Daemon) and scrolling down to the "All Instances" section.
 
Once you have set these, save the file. This is what ```tari``` will use to deploy your template when you're ready.

### Creating a basic template from the available standard templates

The ```new``` command allows you to create a standard template from one of several options that are pre-built into the tool. This allows you to view some basic functions that would be typical in the Ootle and what is required in each template to allow for this functionality.

Via the command line, change the directory to the ```templates``` folder that was created in the previous step, then run the following command:

```bash
../tari new [...yourtemplatename...]
✅ Init configuration and directories
✅ Refresh project templates repository
✅ Refresh wasm templates repository
✅ Collecting available WASM templates
🔎 Select WASM template |
  Fungible Tokens - A Fungible Token template to create your own fungible token.
  NFT - A simple NFT template to create your own.
  Tari Swap - Token swapping template
  Counter - A basic counter example template that can be incremented.
```

You will have an option to select from the available templates to create a template. Note that you will be able to replace this template with your own. The counter template referenced earlier came from the ```Counter``` option.



