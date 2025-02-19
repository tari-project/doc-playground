# The Tari.js Library (WIP)

[tari.js](https://github.com/tari-project/tari.js) is a TypeScript library for web developers to interact with the Ootle (also known as the Tari Network), a second layer chain for the Minotari blockchain. The library uses a provider abstraction to handle connections with two wallets (The Tari Ootle Wallet Daemon via WalletConnect and Metamask via an tari-snap) to provide a consistent API for interacting with the Ootle.

Through the library, developers will be able to build and submit transactions, check accounts, and more.

tari.js uses the ```TariProvider``` export interface for developers to access common functions for interacting with the Ootle via their chosen wallet.

The core elements of the library are:
* ```TariProvider```: An export interface that provides several functions, such as ```getSubstate()```, ```submitTransaction()``` and ```getTransactionResult()```, amongst others. 

* ```TransactionBuilder```: A class for creating transactions for the Ootle that enact certain functions, such as creating new accounts, paying fees or claiming burn amounts.

*  tari.js types: The tari.js types define common structures for various methods and outcomes within the Ootle, such as ```FinalizedResult``` and ```TransactionResult```.

* ```tari_permissions```: A collection of classes and types for managing permissions on resources, access to account balances and more.

## Additional documentation

A big shoutout to [martinserts](https://github.com/martinserts) for putting together documentation regarding Tari.js, which can be found [here](https://tari-project.github.io/tari.js/docs).

## The tari.js sample project

A sample project, [tariswap-ui](https://github.com/mrnaveira/tariswap-ui), has been created for the purpose of demonstrating tari.js implementation as well as leveraging some basic templates. The Tariswap project implements a decentralized exchange smart contract on the Tari network, using a constant-product automated market maker.

### env. Example
```rust
# Template address of the fungible faucet (TestFaucet)
VITE_FAUCET_TEMPLATE=15507b9da703b7f42683e218ff67f2f35debc0c2aa2258b5832a7710070574e1

# Template address of the pool (TariswapPool)
VITE_POOL_TEMPLATE=94dddd6a2e2f5e9c4bf086c6e939570080c7392aa70ebabdbe3460e939283165

# Template address of the pool index (TariswapIndex)
VITE_POOL_INDEX_TEMPLATE=d5a4de8415b576995299a99f012b461cdb84757a6c92e88cb2017d7a38fff292

# Component address of the pool index
VITE_POOL_INDEX_COMPONENT=component_c25b2d005572d3cc488119afb3fd7da3dfbfe61da26a78af133235e6

# Wallet connect project id
VITE_WALLET_CONNECT_PROJECT_ID=78f3485d08b9640a087cbcea000e1f8b

# Tari network id
VITE_NETWORK=36 # Testnet
```

Be sure to update the `vite_network` value to 36 for the `igor` network. The templates for the `tariswap-ui` project are referencing the existing addresses on the ContractNet.

### Example of Updated `package.json`
Your `package.json` should now look similar to this after the dependencies are installed:

```json
{
  "name": "my-tari-project",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "@tari-project/tarijs": "^0.3.1",
    "@tari-project/typescript-bindings": "^1.4.0",
    "@tari-project/wallet_jrpc_client": "^1.4.0",
    "react": "^19.0.0",
    "react-dom": "^19.0.0"
  },
  "devDependencies": {
    "typescript": "^4.5.0",
    "@types/node": "^16.0.0"
  },
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "start": "npm run dev"
  }
}
```

Below is the package.json file specifically from the sample project:

```json
{
  "name": "@tariproject/template-web",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "lint": "eslint . --report-unused-disable-directives --max-warnings 0",
    "preview": "vite preview"
  },
  "dependencies": {
    "@mui/icons-material": "^6.4.3",
    "@mui/material": "^6.4.3",
    "@mui/x-data-grid": "^7.25.0",
    "@tari-project/tarijs": "^0.3.1",
    "@tari-project/typescript-bindings": "^1.4.0",
    "@tari-project/wallet_jrpc_client": "^1.4.0",
    "qrcode.react": "4.2.0",
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "react-icons": "^5.4.0",
    "react-router-dom": "^7.1.5",
    "use-react-router-breadcrumbs": "^4.0.1",
    "zustand": "5.0.3"
  },
  "devDependencies": {
    "@emotion/react": "^11.14.0",
    "@emotion/styled": "^11.14.0",
    "@esbuild-plugins/node-globals-polyfill": "^0.2.3",
    "@esbuild-plugins/node-modules-polyfill": "^0.2.2",
    "@eslint/compat": "^1.2.6",
    "@eslint/eslintrc": "^3.2.0",
    "@eslint/js": "^9.19.0",
    "@types/react": "^19.0.8",
    "@types/react-dom": "^19.0.3",
    "@typescript-eslint/eslint-plugin": "^8.23.0",
    "@typescript-eslint/parser": "^8.23.0",
    "@vitejs/plugin-react-swc": "^3.7.2",
    "eslint": "^9.19.0",
    "eslint-plugin-react-hooks": "^5.1.0",
    "eslint-plugin-react-refresh": "^0.4.18",
    "globals": "^15.14.0",
    "rollup-plugin-node-polyfills": "^0.2.1",
    "vite": "^6.0.11"
  }
}
```

### 



## Using the Tari.js Library
### Hello Ootle Example
We're going to use an example from the sample application to introduce you to the `TariProvider`, Types and the `TransactionBuilder` (for ease of introduction, we'll avoid the Permissions for now and will cover that in a subsequent section).

The file in particular we are interested in is [`counter.ts`](https://github.com/tari-project/tariswap-ui/blob/main/src/faucet.ts). 

We're going to use an example from the sample application to introduce you to the `TariProvider`, Types and the `TransactionBuilder` (for ease of introduction, we'll avoid the Permissions for now and will cover that in a subsequent section).

The file in particular we are interested in is [`faucet.ts`](https://github.com/tari-project/tariswap-ui/blob/main/src/faucet.ts). 

This file provides two main functions: a function to create a faucet with an initial supply of tokens (specified by the user), and a function that allows users to claim free coins from the newly created faucet. To do this, we're going to have to import several dependencies, call upon a template to create the faucet, and submit transactions to enact our requirements. We'll use this example to explain what is happening as we go through the code.

To start, let's look at our imports (take note of the comments):

```typescript
import {
  fromWorkspace, // A type defining a temporary area in memory from the Tari Virtual Machine (TVM)
  TariProvider, // Export interface for interacting with the Ootle via the wallet through JRPC calls.
  TransactionBuilder, // A class that simplifies the process of transaction construction for submission to the Ootle via the wallet.
} from "@tari-project/tarijs";
import * as wallet from "./wallet.ts"; // a helper for submitting and managing transactions via Tari.js
```

Having imported the dependencies, let's look at the first function, the `createFaucet` function

=== "Relevant faucet.ts code section"

    ``` typescript
    export async function createFaucet(
    provider: TariProvider,
    faucetTemplate: string,
    initialSupply: number,
    symbol: string
    ) {
    ```

=== "Full faucet.ts Code "

    ``` typescript
        import {
      fromWorkspace,
      TariProvider,
      TransactionBuilder,
    } from "@tari-project/tarijs";
    import * as wallet from "./wallet.ts";

    export async function createFaucet(
      provider: TariProvider,
      faucetTemplate: string,
      initialSupply: number,
      symbol: string
    ) {
      const account = await provider.getAccount();
      const builder = new TransactionBuilder().callFunction(
        {
          templateAddress: faucetTemplate,
          functionName: "mint_with_symbol",
        },
        [initialSupply, symbol]
      );
      const result = await wallet.submitTransactionAndWaitForResult({
        provider,
        account,
        builder,
        requiredSubstates: [{ substate_id: account.address }],
      });
      return result;
    }

    export async function takeFreeCoins(
      provider: TariProvider,
      faucetComponent: string
    ) {
      const account = await provider.getAccount();
      const builder = new TransactionBuilder()
        .callMethod(
          {
            componentAddress: faucetComponent,
            methodName: "take_free_coins",
          },
          []
        )
        .saveVar("coins")
        .callMethod(
          {
            componentAddress: account.address,
            methodName: "deposit",
          },
          [fromWorkspace("coins")]
        );
      const result = await wallet.submitTransactionAndWaitForResult({
        provider,
        account,
        builder,
        requiredSubstates: [
          { substate_id: account.address },
          { substate_id: faucetComponent },
        ],
      });
      return result;
    }
    ```

Here, we've defined our `createFaucet` function and specified the parameters for the function.