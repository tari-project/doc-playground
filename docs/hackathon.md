# Hackathon Introduction & Instructions (WIP)

Welcome to this internal Tari Hackathon! The purpose of today's session is to allow you to start interacting with the Ootle, developing contracts and apps and generally running the Ootle through it's paces.

The objective of the Hackathon is simple - develop an app that utilises the Ootle. Whether a game, some unique idea or a more "traditional" blockchain service, we hope you'll experiment and see what works (and what doesn't).

## What is a Tari App?

Much focus has been placed on Ootle apps within Universe, a easy to set up, one-click miner for Minotari. The current implementation will serve web apps via the Universe interface in iFrames.

Core to the apps are templates - reusable functionality coded in Rust using the Ootle's `template library, (tari_template_lib)` - which are then compiled as WASM files and commited to the Ootle network. These can then be called to create `components` on the Ootle that can be leveraged by your app.

The templates and created components will serve as the heart of your app's functionality - but that doesn't necessarily mean you need to create them. The Ootle has some pre-existing templates that are already deployed and standard to the experience, such as  `Account` template, and you'll be able to leverage templates uploaded by other developers.

Regardless of whether your leaning on existing templates or creating your own, your web app will need to be able to interact with the Ootle. You can do this easily within your app by using the tari.js library.

So ultimately, your app will consist of:

* Templates that define methods you can call upon to interact with the Ootle through their generated components.
* The `tari.js` TypeScript library to easily interact with the Ootle and deployed templates in your web app via a wallet.
* The actual web app itself, accessible directly through the browser or Universe via iFrame once the URL has been listed. The easist to get started is a React app.
* A wallet that can interact with the Ootle.

## Getting prepped

We've focused on making sure that it's easy to get set up and started, so we've done some pre-setup on our side. However, you're still going to need to configure a couple of items before you can get going.

### Create a React project with the necessary dependencies
The quickest way to set up a new React project is to leverage `vite` to do so. Run the following command below, and select `Y` to continue

```bash
npm create vite@latest
```

Enter a project name, then select `React` from the list of options, and then `Typescript`

```bash
> npx
> create-vite

✔ Project name: … hackathon-app
? Select a framework: › - Use arrow-keys. Return to submit.
    Vanilla
    Vue
❯   React
    Preact
    Lit
    Svelte
    Solid
    Qwik
    Angular
    Others
```

Lastly, choose the `Typescript` variant:

```bash
? Select a variant: › - Use arrow-keys. Return to submit.
❯   TypeScript
    TypeScript + SWC
    JavaScript
    JavaScript + SWC
    React Router v7 ↗
```

Next, you're going to want to update your `package.json` file in the project to include the following items under the `dependencies` section.

Run the following command to do so:

```bash
npm install @tari-project/tari-permissions @tari-project/tarijs @tari-project/typescript-bindings @tari-project/wallet_jrpc_client @tari-project/wallet-daemon-provider
```

Below is what you should be seeing in the package.json file following the above:

```json
    "@tari-project/tari-permissions": "^0.4.0",
    "@tari-project/tarijs": "^0.4.0",
    "@tari-project/typescript-bindings": "^1.4.0",
    "@tari-project/wallet_jrpc_client": "^1.4.0",
    "@tari-project/wallet-daemon-provider": "^0.4.0"
```

### Set up the Ootle Wallet Daemon
You'll need the `tari_dan_wallet_daemon` to proceed. You'll be able to find the latest binaries [here](https://github.com/tari-project/tari-dan/releases). If you don't, look to the end of this section for an alternative means of running the `tari_dan_wallet_daemon`

Once you have it, open your terminal and run the following command:

```bash
./tari_dan_wallet_daemon --help
```

This will provide useful information in addition to command instructions - specifically, the default base directory and the location of the config file.

To start the wallet, run:

```bash
tari_dan_wallet_daemon --network igor
```

If no data exists, it will create a config file and associated data folder using the defaults mentioned in the help section.

!!! tip "Tip"
    You can pass the `-b` command and specify a directory to create a new wallet database and configuration file within your desired location. This can be useful if you are having issues or want to have multiple wallets available. If you are experiencing issues connecting to ContractNet with your wallet, you can use this full command to point directly to the Indexer node on ContractNet: `tari_dan_wallet_daemon --release -- -b [yourfolderforstoringdata] --network igor --indexer-url=http://18.217.22.26:12006/json_rpc`

This will start the wallet, and you should see the following below:

```bash
15:18 INFO  🌐 JSON-RPC listening on 127.0.0.1:9000
15:18 INFO  💤 Stopping JSON-RPC
15:18 INFO  🕸️ HTTP UI started at 127.0.0.1:5100
15:18 INFO  🕸️ HTTP UI listening on 127.0.0.1:5100
```
Navigating to the HTTP URL shown will allow you to access the Ootle wallet's web interface. At this point, you will not have an account (if you're wondering, but wait, what's an account - we'll be explaining some of these items in the following sections.), so you will need to create one. Enter the Account Name and select **"Create Account"** to create the associated account and proceed to the main wallet interface.

![Alt text](/images/tari_dan_wallet_account_creation.png)

This will be your primary wallet to use with either your unique app or the sample app provided. Once created, you'll see the following:

![Alt text](/images/wallet_interface.png)

The important actions here are:
- `Claim Free Tokens`, which will give you the necessary tokens required to perform transactions, submit contracts and perform other activities on the Ootle.
- `Connect with WalletConnect`, which will allow you to connect your wallet with your app for testing purposes (more in a bit).
- `Publish template`, which allows you to select a compiled WASM template for upload, estimate the fees required to submit and prevent you from uploading too large a template.

For now, just `Claim Free Tokens` to get you started.

#### Alternative `tari_dan_wallet_daemon` method

If you have issues with the main method, you can also run the wallet directly from the Ootle repo. In which case, clone the repo: 

```
git clone https://github.com/tari-project/tari-dan.git
```

And run the wallet via `cargo run --bin tari_dan_wallet_daemon --release -- -b data/w3 --network igor --indexer-url=http://18.217.22.26:12006/json_rpc` directly from the Ootle project.

### Hello Ootle - Connecting your Wallet

We've created a `Hello Ootle` app to demonstrate how to connect to your app to the wallet. This app is designed to connect to the Tari blockchain using different wallet methods. It integrates with the Tari ecosystem to manage the user's account and connect it to the app via various wallet options, including WalletConnect, Tari Wallet Daemon, and Tari Universe.

#### Key Features and Components
1. **Main Application Flow**:
   - **Main.tsx** is the entry point where the `App.tsx` is rendered inside a root element.
   - **App.tsx** is the core component of the app that interacts with the state and allows users to connect to a Tari wallet.
   - **useTariProvider** and **useAccount** are hooks that provide access to the Tari provider and account state, respectively.

2. **State Management**:
   - **Zustand** is used to manage the app’s state, including the Tari provider and user account details.
   - **useTariProvider** manages the Tari wallet provider, which is responsible for interacting with the Tari network and fetching account details.
   - **useAccount** manages the user's Tari account. It includes a method to fetch and store the user's account when connected to the wallet.

3. **Wallet Connection**:
   - The app uses **TariConnectButton** to allow users to initiate a connection to a Tari wallet.
   - Upon clicking the connect button, the user is shown a **TariWalletSelectionDialog** where they can choose from different wallet connection methods.

#### Detailed Breakdown of Files

##### 1. **`Main.tsx`** (Main entry point)
```tsx
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './index.css';
import App from './App.tsx';

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <App />
  </StrictMode>,
);
```
- **Function**: Initializes the app, rendering it into a DOM element with the id `root`.
- **StrictMode**: Helps with identifying potential issues in the app during development.

##### 2. **`App.tsx`** (Main React Component)
```tsx
import "./App.css";
import { TariConnectButton } from "./connect/TariConnectButton";
import useTariProvider from "./store/provider";
import useAccount from "./store/account";
import tariLogo from "./assets/tari.svg";

function App() {
  const { provider } = useTariProvider();  // Access the Tari provider
  const { ootleAccount, setOotleAccount } = useAccount();  // Access and manage account state

  const handleOnConnected = async () => {
    await setOotleAccount();  // Fetch the account when connected
  };

  return (
    <>
      <div>
        <a href="https://www.tari.com/" target="_blank">
          <img src={tariLogo} className="logo tari" alt="Tari logo" />
        </a>
      </div>
      <h1>Hello Tari Ootle</h1>
      <TariConnectButton onConnected={handleOnConnected} />
      <div style={{ paddingLeft: "10px" }}>
        {`Provider: ${provider?.isConnected() ? provider.providerName : "not connected"}`}
      </div>
      <div style={{ paddingLeft: "10px" }}>
        {`Account: ${ootleAccount ? ootleAccount?.address : "not found"}`}
      </div>
    </>
  );
}

export default App;
```
- **TariConnectButton**: A button component that initiates the wallet connection process. When clicked, it opens the `TariWalletSelectionDialog`.
- **Provider and Account Info**: Displays the connection status of the Tari provider and the current user's account details (if available).

##### 3. **`account.ts`** (Account Store)
```ts
import { create } from "zustand";
import useTariProvider from "./provider.ts";
import { Account } from "@tari-project/tarijs";

interface State {
  ootleAccount?: Account;
}

interface Actions {
  setOotleAccount: () => Promise<void>;
}

const initialState: State = {
  ootleAccount: undefined,
};

export const useAccount = create<OotleWalletStoreState>()((set) => ({
  ...initialState,
  setOotleAccount: async () => {
    const provider = useTariProvider.getState().provider;
    try {
      if (!provider) {
        return;
      }
      const account = await provider.getAccount();
      set({
        ootleAccount: account,
      });
    } catch (error) {
      console.error("Could not set the Ootle account: ", error);
    }
  },
}));

export default useAccount;
```
- **State**: The `ootleAccount` is stored in the app’s state, which is managed by Zustand. It contains details of the user's Tari account.
- **Actions**: The `setOotleAccount` function fetches the account details from the Tari provider and updates the store's state.

##### 4. **`provider.ts`** (Tari Provider Store)
```ts
import { create } from "zustand";
import { TariProvider } from "@tari-project/tarijs";

export interface ProviderStore<TProvider extends TariProvider> {
  provider: TProvider | null;
  setProvider(provider: TProvider): void;
}

const useTariProvider = create<ProviderStore<TariProvider>>()((set) => ({
  provider: null,
  setProvider(provider) {
    set({ provider });
  },
}));

export default useTariProvider;
```
- **State**: Stores the current Tari provider instance (e.g., WalletConnect, Wallet Daemon).
- **setProvider**: A function that updates the `provider` state when a wallet is selected and connected.

##### 5. **`settings.ts`** (Settings Store)
```ts
import { create } from "zustand";
import { createJSONStorage, persist } from "zustand/middleware";

export interface Settings {
  template: string | null;
}

export interface SettingsStore {
  settings: Settings;
  setSettings(settings: Settings): void;
}

const useSettings = create<SettingsStore>()(
  persist<SettingsStore>(
    (set) => ({
      settings: { template: null },
      setSettings(settings) { set({ settings }) },
    }),
    {
      name: "settings",
      storage: createJSONStorage(() => localStorage),
    }
  ),
);

export default useSettings;
```
- **Settings**: Stores user preferences/settings (e.g., template).
- **Persistence**: Uses the `zustand` middleware to persist settings in local storage across sessions.

##### 6. **`TariConnectButton.tsx`** (Connect Button Component)
```tsx
import * as React from "react";
import Button from "@mui/material/Button";
import TariLogoWhite from "./content/tari-logo-white.svg";
import { TariWalletSelectionDialog } from "./TariWalletSelectionDialog";
import useTariProvider from "../store/provider.ts";
import { TariProvider } from "@tari-project/tarijs";

export function TariConnectButton(props: Props) {
  const { provider, setProvider } = useTariProvider();
  const { onConnected } = props;
  const [walletSelectionOpen, setWalletSelectionOpen] = React.useState(false);

  const handleConnectClick = () => {
    setWalletSelectionOpen(true);
  };

  const onWalletSelectionClose = () => {
    setWalletSelectionOpen(false);
  };

  const handleOnConnected = (provider: TariProvider) => {
    setProvider(provider);
    onConnected?.(provider);
  };

  return (
    <>
      <Button variant="contained" onClick={handleConnectClick}>
        <img width="30px" height="30px" src={TariLogoWhite} />
        <div style={{ paddingLeft: "10px" }}>
          {provider?.isConnected() ? "Connected" : "Connect"}
        </div>
      </Button>
      <TariWalletSelectionDialog
        open={walletSelectionOpen}
        onClose={onWalletSelectionClose}
        onConnected={handleOnConnected}
      />
    </>
  );
}
```
- **TariConnectButton**: This button opens a dialog for the user to select a wallet connection method. Once the wallet is selected and connected, the provider state is updated.

##### 7. **`TariWalletSelectionDialog.tsx`** (Wallet Selection Dialog)
The code provided is a **React component** (`TariWalletSelectionDialog`) that handles the display of a modal dialog for selecting a wallet connection method. The user can choose from different wallet providers like **WalletConnect**, **Tari Wallet Daemon**, or **Tari Universe** (depending on the feature flags in the environment configuration). Here’s a breakdown of the code and its components:

1. **Imports**

- **Material UI Components**: The code uses several Material UI (MUI) components such as `Dialog`, `Box`, `Typography`, `Stack`, `Card`, `Grid`, and `IconButton`. These are used for layout, styling, and UI elements in the dialog.
- **Icons**: It imports `CloseIcon` from MUI icons for the close button.
- **Tari-related Imports**: There are multiple imports from the `@tari-project/tarijs` and `@tari-project/tari-permissions` packages. These are used to define and handle different types of wallet providers and permissions.
  
2. **Constants for Environment Variables**

```js
const WALLET_CONNECT_PROJECT_ID = import.meta.env.VITE_WALLET_CONNECT_PROJECT_ID || null;
const WALLET_DAEMON_ENABLED = import.meta.env.VITE_WALLET_DAEMON_ENABLED || false;
const TARI_UNIVERSE_ENABLED = import.meta.env.VITE_TARI_UNIVERSE_ENABLED || false;
const WALLET_DAEMON_JRPC = import.meta.env.VITE_WALLET_DAEMON_JRPC || "http://127.0.0.1:12009/json_rpc";
```

These constants are set from environment variables using `import.meta.env` which provides access to the environment during build time (for example, when using Vite as a bundler). These constants define:

- **WALLET_CONNECT_PROJECT_ID**: The ID for the WalletConnect project (for connecting with WalletConnect).
- **WALLET_DAEMON_ENABLED**: Whether or not the Wallet Daemon functionality is enabled.
- **TARI_UNIVERSE_ENABLED**: Whether or not Tari Universe functionality is enabled.
- **WALLET_DAEMON_JRPC**: The URL for the wallet daemon's JSON-RPC endpoint.

3. **Permission Setup**

The `walletDaemonPermissions` object is created by instantiating the `TariPermissions` class and adding multiple permissions. These permissions define what actions the app can perform when interacting with the wallet daemon. Here, permissions such as:

- **Admin**: The app has administrative access.
- **Key List, Account Info, Transactions Get, Substates Read**: The app can interact with key management, get account info, read transactions, and interact with substate data.
  
Optional permissions are also defined but left empty (`walletDaemonOptionalPermissions`), which could be populated if needed in future features.

4. **TariWalletSelectionDialog Component**

This is the main component that renders the wallet selection dialog. It accepts three props:

- `open`: A boolean indicating whether the dialog is open or not.
- `onConnected`: A callback function to handle the wallet provider once the user selects a provider.
- `onClose`: A callback function to close the dialog when the user cancels or completes an action.

* `handleClose` function:
This function simply triggers the `onClose` callback to close the dialog when called.

* `onWalletDaemonClick`, `onWalletConnectClick`, and `onTariUniverseClick` functions:
Each of these functions is triggered when the user selects a specific wallet provider. They do the following:

* **WalletDaemon**: This function prepares the `WalletDaemonTariProvider` with the necessary permissions and the JRPC URL (from `WALLET_DAEMON_JRPC`). It then calls the `onConnected` callback with the `walletDaemonProvider` instance.
  
* **WalletConnect**: This function initializes the `WalletConnectTariProvider` with the project ID (`WALLET_CONNECT_PROJECT_ID`) and calls the `onConnected` callback after the connection is established.

* **Tari Universe**: This function creates a `TariUniverseProvider` with the required permissions and calls the `onConnected` callback with the provider.

Each of these functions first invokes `handleClose()` to close the dialog, and then proceeds to connect to the selected wallet.

5. **Dialog and Layout**

The `Dialog` component from MUI is used to create the modal window for wallet selection. It contains the following structure:

* **Stack**: This is a flex container used to lay out the header section (title and close button).
  - The title "Connect a wallet" is rendered inside a `Typography` component.
  - An `IconButton` is used to close the dialog, which triggers the `handleClose` function when clicked.

* **Divider**: A simple horizontal line separating the header from the body of the dialog.

* **Grid**: The `Grid` layout component is used to display wallet connection options in a responsive manner. It arranges multiple `WalletConnectionMethodCard` components in a grid layout, and the `spacing` prop controls the space between them.

6. **WalletConnectionMethodCard Component**

This is a reusable component that represents each wallet provider option in the dialog. It takes the following props:

- `img`: The image to display as the logo of the wallet provider.
- `text`: The text to display under the logo (e.g., "WalletConnect", "Tari Wallet Daemon").
- `callback`: The function to call when the user clicks the card (either `onWalletConnectClick`, `onWalletDaemonClick`, or `onTariUniverseClick`).

The component uses a **Card** layout with a `CardContent` inside. When the card is clicked, the `callback` function is invoked, which triggers the connection to the selected wallet provider.

7. **Conditional Rendering of Connection Options**

* **WalletConnect** is always shown.
* **Tari Wallet Daemon** is conditionally displayed based on the value of `WALLET_DAEMON_ENABLED`. If it's enabled (from the environment variable), the `WalletConnectionMethodCard` for this provider is shown.
- **Tari Universe** is similarly conditionally rendered based on the `TARI_UNIVERSE_ENABLED` environment variable.

This code provides a **wallet selection dialog** with three potential wallet connection methods:
- **WalletConnect**: Uses WalletConnect to connect with external wallets.
- **Tari Wallet Daemon**: Uses a local wallet daemon instance.
- **Tari Universe**: Uses a remote Tari Universe provider for connecting.

The dialog is structured with Material UI components for layout and styling, and the connections are established using the Tari project's API (`@tari-project/tarijs`). The app is highly configurable through environment variables, allowing different wallet options to be enabled or disabled.

## Digging into the sample project

* Ideally, you'll need to set up Rust in your desired Integrated Development Environment (IDE). If you're unsure where to start, our [Setting Up Your Development Environment Guide](https://tari.com/lessons/08_setting_up_development_environment) is a good starting point

* The Ootle Wallet Daemon. This is your wallet on the second layer and how you will be interacting with ContractNet via Tari.js (more on this in a bit).

* Once set up, you'll need to grab some free test tokens so you can submit transactions, deploy templates and more.

* The Tari.js library, [located here](https://github.com/tari-project/tari.js/). This typescript library should help you perform some of the common functions and calls for the wallet easily via jRPC. A detailed guide for the library can be found here: [Tari.JS Library Introduction](tarijs_library_intro.md)

* **Optional** We have created a sample project (A decentralized exchange smart contract on the Tari network. The main functions include swapping tokens, adding liquidity, and removing liquidity.) that leverages Tari.js, so you have some practical working examples. 

In addition, we'll cover some basic implementations and actions you can perform with code examples so you can get started.








## But firstly - what is the Ootle?

If you're unfamiliar with the Ootle, we'd like to encourage you to read the [Tari Introduction](tari-introduction.md), which gives a quick, high-level overview of Tari, the Ootle and it's various components.