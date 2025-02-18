# Getting Started with Template

Development on the Ootle, at its most basic, begins with the templates and smart contracts generated from them. These are the foundational blocks required to perform any Ootle specific functions.

But what are these? Think of a template as a specification for a smart contract. From a template, a user can create a smart contract for anything from a simple counter system to a fully-fleged NFT series. This contract then exists on its own, independent of the template, and can be used to perform specific actions. 

The community has focused on developing many templates for common activities envisioned for the Ootle with security and stability in mind. Rather than reinvent the wheel every time you would like to, for example, create an NFT series, you can leverage the templates that have been incorporated into the template library. However, for your own unique services or requirements, you may need to create your own.

To develop within the Ootle (both apps or Tari templates), you're going to need the following:

* The Tari CLI for quick scaffolding of template projects
* The `tari_template_lib` 


This guide assumes some basic knowledge of Git, Cargo and Rust.

## Basic Concepts of the Ootle

- **Runtime Environment**: The runtime environment handles various elements such as buckets, proofs, substates, and components. It provides methods for manipulating these elements and managing their interactions. This includes the Tari Virtual Machine (TVM)
- **Workspace**: Manages variables and proofs, providing methods to insert and retrieve indexed values. It's a localised instance of the variables required when performing a transaction against a smart contract that exists within the TVM.
- **Component**: A reusable and addressable entity with associated state and methods. This exists as a substate on the Ootle, that can be called when required via a unique address.
- **Bucket**: Handles resources within the component or workspace. Buckets are used to securely move data between vaults.
- **Vault**: Securely stores resources and manages access control. An example of a vault is a collection of tokens, which needs to be updated as people withdraw or deposit more tokens into the vault.

## List of available tempaltes

The following templates are already available on the network:

* 


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

These are the means that we are going to intereact with the data type we've created in the Counter structure. We can create a new counter component on the network (so we can have multiple Counter components running at each of their component addresses), a method to call the current value of the counters, and a method to increase any counter's value by 1.

## Available Templates

The following templates have already been deployed to the network by default:

* 