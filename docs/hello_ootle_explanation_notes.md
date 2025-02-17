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