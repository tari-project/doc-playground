# Getting Started on your First Project

We're going to use the React standard project as a base for our first project. This will minimise the amount of work that's required to dive into the core topics and get started.

You'll be using the Tari.js library for most of your interactions on the Ootle. The tari.js library has several providers that provide ready access to the Ootle via Universe, the OotleWalletDaemon or WalletConnect.

We'll be using WalletConnect to connect to our Ootle Wallet.

## Creating the Logic to connect to your Ootle wallet via WalletConnect

In VS Code, go ahead and open up the `App.tsx` file. We'll be adding a basic button to call the WalletConnect provider and connect your wallet to your app.

First, you'll want to add the following line to the beginning of the file (the line to add is highlighted)

```typescript linenums="1" hl_lines="1"
import { WalletConnectTariProvider } from "@tari-project/wallet-connect-provider";
import reactLogo from './assets/react.svg';
import viteLogo from '/vite.svg';
import './App.css';
```

This will import the provider for use within your app. Next, you'll want to add these lines - don't worry, we'll go through each of these shortly:

```typescript linenums="1"
function App() {
  const [isConnected, setIsConnected] = useState(false); // Track wallet connection state
  const [errorMessage, setErrorMessage] = useState<string | null>(null); // Track any connection errors
  const [accountAddress, setAccountAddress] = useState<string | null>(null); // Store the account address

// Calling the WalletConnect ProjectID required for WalletConnect to work  
  const projectId = "1825b9dd9c17b5a33063ae91cbc48a6e";
  const provider = new WalletConnectTariProvider(projectId);

  const connectToWallet = async () => {
    try {
      // Try connecting to the wallet
      await provider.connect();
      setIsConnected(true);
      
      // Fetch account address after successful connection
      const account = await provider.getAccount();
      setAccountAddress(account.address);
      setErrorMessage(null);
    } catch (error) {
      console.error("Error connecting to wallet:", error);
      setErrorMessage("Failed to connect to wallet.");
      setIsConnected(false);
    }
  };
```

So let's start with the first line:

```javascript
const [isConnected, setIsConnected] = useState(false); // Track wallet connection state
```
This initializes a state variable `isConnected` with an initial value of `false`. The `setIsConnected` function is used to update the state when the wallet connection state changes (e.g., after a successful connection or disconnection).

```javascript
const [errorMessage, setErrorMessage] = useState<string | null>(null); // Track any connection errors
```
This creates a state variable `errorMessage` that will either hold an error message string or be `null`. `setErrorMessage` is the function used to update the `errorMessage` state (e.g., if there's an error connecting to the wallet, the error message will be stored here).

```javascript
const [accountAddress, setAccountAddress] = useState<string | null>(null); // Store the account address
```
This initializes the `accountAddress` state variable as `null`.  It will hold the account address of the wallet after a successful connection, and `setAccountAddress` is used to update this state.

```javascript
const projectId = "1825b9dd9c17b5a33063ae91cbc48a6e";
const provider = new WalletConnectTariProvider(projectId);
```
This defines a constant `projectId` with a string value. This `projectId` is required by WalletConnect (a library that allows users to connect their wallets to decentralized applications) to authenticate the connection. This is the Tari projectID for WalletConnect.

The provider variable initializes a new `provider` using `WalletConnectTariProvider`, which is a custom provider for interacting with the WalletConnect API. The `projectId` is passed to the provider so that it can authenticate and connect to the right WalletConnect project.

```javascript
const connectToWallet = async () => {
```
This declares an asynchronous function `connectToWallet`. The `async` keyword allows you to use `await` inside the function to wait for promises (e.g., asynchronous operations like connecting to a wallet or fetching data).

```javascript
  try {
```
This begins a `try` block to catch any errors that may occur during the wallet connection process.

```javascript
    await provider.connect();
```
This attempts to establish a connection to the wallet by calling `connect()` on the `provider`. The `await` keyword pauses the function until the connection attempt is either successful or throws an error.

```javascript
    setIsConnected(true);
```
If the connection is successful, this updates the `isConnected` state to `true`, indicating that the wallet is now connected.

```javascript
    const account = await provider.getAccount();
```
This calls the `getAccount()` method on the `provider` to retrieve the account associated with the wallet. It uses `await` to wait for the account details to be fetched.

```javascript
    setAccountAddress(account.address);
```
This updates the `accountAddress` state with the account address retrieved from `provider.getAccount()`. This is the address of the connected wallet.

```javascript
    setErrorMessage(null);
```
If the wallet connection is successful, any previous error message is cleared by setting `errorMessage` to `null`.

```javascript
  } catch (error) {
```
This catches any errors that occur within the `try` block. If an error happens during the connection attempt, the code in the `catch` block is executed.

```javascript
    console.error("Error connecting to wallet:", error);
```
If an error occurs, it logs the error to the console with the message "Error connecting to wallet:" to help with debugging.

```javascript
    setErrorMessage("Failed to connect to wallet.");
```
This sets the `errorMessage` state to a user-friendly message: "Failed to connect to wallet." This message can be displayed in the UI to notify the user that there was an issue with the wallet connection.

```javascript
    setIsConnected(false);
```
Since the connection failed, this updates the `isConnected` state to `false` to indicate that the wallet is not connected.

## Adding the button and logic to your app

Next, in the `return` section, you'll want to copy and paste the following, with the relevant sections highlighted. The comments indicate what the code does:

```javascript linenums="1" hl_lines="13-33"
  return (
    <>
      <div>
        <a href="https://vite.dev" target="_blank">
          <img src={viteLogo} className="logo" alt="Vite logo" />
        </a>
        <a href="https://react.dev" target="_blank">
          <img src={reactLogo} className="logo react" alt="React logo" />
        </a>
      </div>
      <h1>Vite + React</h1>

      {/* Display the connection status */}
      <div className="card">
        {/* Calls the connectToWallet asynchronus function */}
        <button onClick={connectToWallet} className="connect-button">
          Connect to Wallet
        </button>
        <p>
        {/* Displays the isConnected value once fethed from connectToWallet */}
         {isConnected ? (
            <span style={{ color: 'green' }}>Wallet is connected!</span>
          ) : (
            <span style={{ color: 'red' }}>Wallet is not connected</span>
          )}
        </p>
        {/* Displays the account address if the value of the accountAddress variable is not null */}
        {accountAddress && (
          <p>Account Address: {accountAddress}</p>
        )}
        {/* Displays an error message if the value of the errorMessage variable is not null */}
        {errorMessage && (
          <p style={{ color: 'red' }}>{errorMessage}</p>
        )}
      </div>

      <p className="read-the-docs">
        Click on the Vite and React logos to learn more
      </p>
    </>
  );
}
```

The above adds a button to the existing React project, just below the React and Vite logos.
