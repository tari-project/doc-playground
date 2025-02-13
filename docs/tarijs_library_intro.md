# The Tari.js Library (WIP)

[tari.js](https://github.com/tari-project/tari.js) is a TypeScript library for web developers to interact with the Ootle (also known as the Tari Network), a second layer chain for the Minotari blockchain. The library uses a provider abstraction to handle connections with two wallets (The Tari Ootle Wallet Daemon via WalletConnect and Metamask via an tari-snap) to provide a consistent API for interacting with the Ootle.

Through the library, developers will be able to build and submit transactions, check accounts, generate 

tari.js uses the ```TariProvider``` export interface for developers to access common functions for interacting with the Ootle via their chosen wallet.

The core elements of the library are:
* ```TariProvider```: An export interface that provides several functions, such as ```getSubstate()```, ```submitTransaction()``` and ```getTransactionResult()```, amongst others. 

* ```TransactionBuilder```: A class for creating transactions for the Ootle that enact certain functions, such as creating new accounts, paying fees or claiming burn amounts.

*  tari.js types: The tari.js types define common structures for various methods and outcomes within the Ootle, such as ```FinalizedResult``` and ```TransactionResult```.

* ```tari_permissions```: A collection of classes and types for managing permissions on resources, access to account balances and more.

We'll discuss some of the common implementations of the above further in this document alongside a sample project. For a full breakdown of the related items, please visit their associated pages for more detailed information on each function, class, type, etc:

* [TariProvider](tariproivder.md)
* [TransactionBuilder](transactionbuilder.md)
* [Types](tarijs_types.md)
* [Tari Permissions](tari_permissions.md)

## The tari.js sample project

A sample project, [tariswap-ui](https://github.com/mrnaveira/tariswap-ui), has been created for the purpose of demonstrating tari.js implementation as well as some basic templates. The Tari Swap project is an application for creating various pools of tokens to be swapped, with pools being funded by users who receive "Liquidity Tokens" in return. This is discussed in detail in the current [Hackathon Guide](hackathon.md).

## Steps to Integrate **Tari.js** into Your Project

1. **Ensure Your Project Uses Node.js and npm/yarn**: Make sure you are working in a **Node.js** environment and you have either **npm** or **yarn** installed to manage your project dependencies. If not, [install Node.js](https://nodejs.org/) (which includes npm).
   
2. **Initialize Your Project** (If Not Already Done): If your project doesn't have a `package.json` file, create it by running:
     ```bash
     npm init -y
     ```

3. **Install Tari.js and Dependencies**: You'll need to install **Tari.js** and its associated dependencies (such as `typescript-bindings` and `wallet_jrpc_client`).
   
   Run the following npm command to install **Tari.js** and other required packages:
   ```bash
   npm install @tari-project/tarijs @tari-project/typescript-bindings @tari-project/wallet_jrpc_client
   ```

   This will add these packages to the `dependencies` section of your `package.json` file automatically.

4. **(Optional) Install TypeScript**: If you're using TypeScript in your project, you should install **TypeScript** and the necessary types for your environment. 
   ```bash
   npm install --save-dev typescript @types/node
   ```
   - You can create a `tsconfig.json` file using `tsc --init`, if needed.

5. **Install React (If Using React)**:
   - If your project uses **React**, install **React** and **React-DOM** by running:
   ```bash
   npm install react react-dom
   ```
   
5. **(Optional) Configure TypeScript**:
   - If you're using **TypeScript**, make sure to add any types or configuration for **Tari.js** if needed. The Tari libraries should already include types, but ensure your `tsconfig.json` is properly set up:
   ```json
   {
     "compilerOptions": {
       "target": "es6",
       "module": "es6",
       "moduleResolution": "node",
       "esModuleInterop": true,
       "strict": true,
       "skipLibCheck": true
     }
   }
   ```
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

## Using the Tari.js Library
We're going to use an example from the sample application to introduce you to the `TariProvider`, Types and the `TransactionBuilder` (for ease of introduction, we'll avoid the Permissions for now and will cover that in a subsequent section)

The file in particular we are interested in is [`faucet.ts`](https://github.com/mrnaveira/tariswap-ui/blob/main/src/faucet.ts). 

This file provides two main functions: a function to create a faucet with an initial supply of tokens (specified by the user), and a function that allows users to claim free coins from the newly created faucet. To do this, we're going to have to import several dependencies, call upon a template to create the faucet, submit transactions to enact our requirements. We'll use this example to explain what is happening as we go through the code.

To start, let's look at our imports:

```typescript
import {
  fromWorkspace, // A type defining a temporary area in memory from the Tari Virtual Machine (TVM)
  TariProvider, // Export interface for interacting with the Ootle via the wallet through JRPC calls.
  TransactionBuilder, // A class that simplifies the process of transaction construction for submission to the Ootle via the wallet.
} from "@tari-project/tarijs";
import * as wallet from "./wallet.ts"; 
```

Having imported the dependencies, let's look at the first function, the createFaucet

=== "Relevant faucet.ts code section"

    ``` typescript
    export async function createFaucet(
    provider: TariProvider,
    faucetTemplate: string,
    initialSupply: number,
    symbol: string
)   {
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

```typescript

```



# The Ootle - Core Concepts

Your understanding of the interactions that are possible on the Ootle will be aided by reviewing the [Tari RFC-0330/Cerberus page](https://rfc.tari.com/RFC-0330_Cerberus), with the most important element concerning substates - what they are and the various types of substates. We'll summarise below but the RFC document is well worth the time.

In short, The Ootle breaks itself up into 2^256 substate slots which can be used to store one of several types of states. We won't cover all of them here:

* ```Template```: Templates are parameterised smart contracts. Templates are intended to be well-tested, secure, reusable components for building and running smart contracts on the DAN. It is the result of a compiled WASM file submitted to the Ootle. The template is a special ```Component``` substate. An example would be the built-in Non-fungible token (NFT) template, which can be used to create an NFT series.
* ```Component```: An initialisation of a template. In essence, it is also a smart contract, but one that has been created as a result of calling the template. Continuing the example, a user would call the NFT template, creating a new NFT series. The rules and functions available to this NFT series would be then form the component. 
* ```Resource```: This is for representing a token on the network. The resource is purely the identifier and definition of the token on the network. 
* ```Vault```: This is a core part of the ```resource``` substate, which contains all the data associated with the token. This includes things like the current balance of the resources, associated accounts, who owns which resources and more.

Developers will, via submitted transactions, interact with these substates to provide functionality via their app, whatever that may be.

