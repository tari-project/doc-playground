# Introduction

We'll be modifying your existing project to add a "Create Counter" button with ability to create a new Counter on the Ootle. In order to do this, we'll need to add the following:

- Import some of the pre-existing functions for transactions from the TariProvider in tari.js
- Add variables to track the submission and result of the transaction
- Add a function to our app to call the Transaction Builder
- Build a transaction with various required data, such the fees and the template address.
- Create a new counter and report the location of the counter on the Ootle

Let's go through the steps.

## Import the Functions
=== "Import Functions"
    ```typescript linenums="3"
    import { Amount, TransactionBuilder, buildTransactionRequest, Network, submitAndWaitForTransaction } from "@tari-project/tarijs";
    ```

=== "Import Functions Location in Code" 
    ```typescript linenums="1" hl_lines="3"
    import React, { useState } from "react";
    import { WalletConnectTariSigner } from "@tari-project/wallet-connect-signer";
    import { Amount, TransactionBuilder, buildTransactionRequest, Network, submitAndWaitForTransaction } from "@tari-project/tarijs";
    import reactLogo from './assets/react.svg';
    import viteLogo from '/vite.svg';
    import './App.css';
    ```

This imports the necessary functions from Tari.js in order to build a transaction, submit it to the network and await the results.

## State Management for Transaction:**
=== "Adding State Management"
    ```typescript linenums="11"
    const [isSubmitting, setIsSubmitting] = useState(false);  // Track submission state
    const [txResult, setTxResult] = useState<any>(null); // Store the result of the transaction
    ```

=== "State Management Location in Code" 
    ```typescript linenums="7" hl_lines="5-6"
    function App() {
        const [isConnected, setIsConnected] = useState(false); // Track wallet connection state
        const [errorMessage, setErrorMessage] = useState<string | null>(null); // Track any connection errors
        const [accountAddress, setAccountAddress] = useState<string | null>(null); // Store the account address
        const [isSubmitting, setIsSubmitting] = useState(false); // Track submission state
        const [txResult, setTxResult] = useState<any>(null); // Store transaction result
    ```

These states are used to track the submission process and to store the result of the transaction once it is completed.







To call a function on an existing template to create a new component from that template using the `TransactionBuilder` and `callFunction` method in `@tari-project/tarijs`, here's a step-by-step guide:

### Step 1: Set up your environment
First, make sure you have the necessary imports and the `TransactionBuilder` instance set up:

```javascript
import {
  TransactionBuilder,
  buildTransactionRequest,
  Network,
  submitAndWaitForTransaction,
} from "@tari-project/tarijs";

// Initialize the Transaction Builder
let builder = new TransactionBuilder();
```

### Step 2: Define the Template and Function
You need the address of the template that you want to instantiate. In this example, we’ll call the `new` function of a template to create a new component.

```javascript
const templateAddress = "resource_217d58767480fbaa48693e11de34baf7fd727e33ee7627f0c4e5b78def378e57"; // Example template address
```

### Step 3: Call the Template's Function
Now, you can use `callFunction` to invoke the function of the template and pass any required parameters to it. For this example, we assume the function we want to call is the `new` function of the template.

```javascript
builder = builder.callFunction(
  {
    templateAddress, // Address of the template
    functionName: "new", // Function to call (typically for instantiating a new component)
  },
  [1, 2, 3] // Parameters to pass to the function (replace with your actual parameters)
);
```

In this case, we’re calling the `new` function and passing `[1, 2, 3]` as arguments. These could be any values that the `new` function expects (like initial states, amounts, etc.).

### Step 4: Build the Transaction
Once you’ve added all the necessary instructions to your `TransactionBuilder`, you can build the transaction:

```javascript
const transaction = builder.build();
```

### Step 5: Build the Transaction Request
Next, create the transaction request. This involves specifying the account that will execute the transaction, the network, and any other necessary parameters:

```javascript
const account = await provider.getAccount(); // Get the account executing the transaction
const isDryRun = false;
const network = Network.LocalNet; // Network where the transaction will be executed
const requiredSubstates = []; // Can be empty if not required

const submitTransactionRequest = buildTransactionRequest(
  transaction,
  account.account_id,
  requiredSubstates,
  undefined, // Obsolete inputRefs
  isDryRun,
  network
);
```

### Step 6: Execute the Transaction
Finally, execute the transaction and wait for it to be processed:

```javascript
const txResult = await submitAndWaitForTransaction(provider, submitTransactionRequest);
```

### Full Example Code:
Here’s how the entire process looks:

```javascript
import {
  TransactionBuilder,
  buildTransactionRequest,
  Network,
  submitAndWaitForTransaction,
} from "@tari-project/tarijs";

// Initialize the Transaction Builder
let builder = new TransactionBuilder();

// Template address for the new component
const templateAddress = "resource_217d58767480fbaa48693e11de34baf7fd727e33ee7627f0c4e5b78def378e57"; 

// Call the template function to create a new component
builder = builder.callFunction(
  {
    templateAddress, 
    functionName: "new",
  },
  [1, 2, 3] // Example parameters for the "new" function
);

// Build the transaction
const transaction = builder.build();

// Get the account to execute the transaction
const account = await provider.getAccount();
const isDryRun = false;
const network = Network.LocalNet; // Network

// Build the transaction request
const submitTransactionRequest = buildTransactionRequest(
  transaction,
  account.account_id,
  [], // No required substates in this case
  undefined, // Obsolete
  isDryRun,
  network
);

// Execute the transaction
const txResult = await submitAndWaitForTransaction(provider, submitTransactionRequest);
```

### Explanation:
- **Template Address**: The `templateAddress` is the identifier of the template you want to instantiate.
- **`callFunction`**: This method is used to invoke the `new` function of the template, which will create a new component.
- **Transaction Building**: The instructions are added to the builder, and the transaction is built and submitted.
  
This is a simple example to demonstrate how to call a function from a template to create a new component. Replace the parameters and template address with actual values depending on your use case.

Certainly! Let's break down what `buildTransactionRequest` and `submitAndWaitForTransaction` do in the context of the code and how they work.

### 1. `buildTransactionRequest`

The `buildTransactionRequest` function is used to prepare the **transaction request** that will be sent to the network for execution. A transaction request includes all the details necessary for a transaction, such as the instructions to be executed, the account details, network settings, and additional parameters like required substates or fee instructions.

In the example, `buildTransactionRequest` is called with several parameters to assemble the complete request for the transaction:

#### Parameters for `buildTransactionRequest`:
- **`transaction`**: This is the transaction object built with `TransactionBuilder`. It contains the instructions (e.g., calling functions or methods on components) and other data such as fees.
- **`account_id`**: The account ID of the sender (the account that will execute the transaction).
- **`requiredSubstates`**: A list of substates that the transaction will modify or interact with. This could include, for example, the account state or component state. (In the code above, it's an empty array, but in practice, it might contain specific state references.)
- **`inputRefs`**: This field is obsolete in this case and typically was used for references to inputs that are involved in the transaction. You can leave it `undefined` in this case.
- **`isDryRun`**: If `true`, the transaction will be validated but not actually submitted (dry run). If `false`, the transaction will be executed for real.
- **`network`**: The network where the transaction will be executed (e.g., LocalNet, NextNet, etc.).

**Usage**:
```javascript
const submitTransactionRequest = buildTransactionRequest(
  transaction,           // The built transaction
  account.account_id,    // The account executing the transaction
  requiredSubstates,     // List of required substates
  undefined,             // Obsolete inputRefs
  isDryRun,              // Whether it's a dry run (true for validation only)
  network                // The network to execute the transaction on
);
```

`buildTransactionRequest` essentially packages the transaction instructions and all associated metadata into a format that can be sent to the network for processing.

---

### 2. `submitAndWaitForTransaction`

Once the transaction request is built using `buildTransactionRequest`, the next step is to **submit** that transaction to the network and **wait for its execution** (and confirmation). This is where `submitAndWaitForTransaction` comes into play.

#### Purpose:
- `submitAndWaitForTransaction` sends the transaction to the network.
- It then waits for the transaction to be processed and confirmed.
- It ensures the result (whether successful or failed) is returned after waiting for the transaction's final state.

#### Usage:
```javascript
const txResult = await submitAndWaitForTransaction(provider, submitTransactionRequest);
```

#### Parameters for `submitAndWaitForTransaction`:
- **`provider`**: This is the object responsible for communicating with the network. It can be an instance of a provider or client that interfaces with the Tari network, allowing you to send transactions and interact with the blockchain.
- **`submitTransactionRequest`**: This is the request built using `buildTransactionRequest`. It contains all the instructions and metadata for the transaction that will be executed.

#### What happens inside `submitAndWaitForTransaction`:
1. It **submits the transaction** to the network using the provider.
2. It then **waits for the transaction to be processed** by the network. This might involve checking the transaction status, confirming it has been successfully executed, and retrieving any result (e.g., the outcome of a function call).
3. Once the transaction is processed and confirmed, it **returns the result**, which could include information like the transaction's success/failure, any changes in state, or transaction-specific data.

The `await` keyword makes it an **asynchronous operation**, meaning the code will pause at this point until the transaction is complete and the result is returned. This is crucial for ensuring that the transaction has been successfully processed before moving on to the next step.

---

### In Summary:
- **`buildTransactionRequest`** is responsible for assembling all the details and instructions into a single, structured request for the transaction.
- **`submitAndWaitForTransaction`** submits that request to the network and waits for the transaction to be processed and confirmed, ensuring that you can safely proceed once the transaction is finalized.

These two functions work together to ensure that a transaction is correctly built, submitted, and tracked until its completion on the network.






--========----
Certainly! I'll walk you through incorporating the transaction creation and submission logic into your existing React application. The key steps will involve adding the necessary code to handle the transaction building, submitting the transaction, and providing feedback to the user.

### Modifying Your React App:

The following code snippet will integrate the transaction logic into your existing app. I'll explain each part of the new code added.

### Full Modified Application:

```tsx
import React, { useState } from "react";
import { WalletConnectTariProvider } from "@tari-project/wallet-connect-provider";
import { Amount, TransactionBuilder, buildTransactionRequest, Network, submitAndWaitForTransaction } from "@tari-project/tarijs";
import reactLogo from './assets/react.svg';
import viteLogo from '/vite.svg';
import './App.css';

function App() {
  const [isConnected, setIsConnected] = useState(false); // Track wallet connection state
  const [errorMessage, setErrorMessage] = useState<string | null>(null); // Track any connection errors
  const [accountAddress, setAccountAddress] = useState<string | null>(null); // Store the account address
  const [isSubmitting, setIsSubmitting] = useState(false); // Track submission state
  const [txResult, setTxResult] = useState<any>(null); // Store transaction result

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

  const createAndSubmitTransaction = async () => {
    if (!accountAddress) {
      setErrorMessage("Account address is not available.");
      return;
    }

    setIsSubmitting(true);  // Start the transaction submission process
    setErrorMessage(null);

    try {
      // Create the fee amount (e.g., 2000 units)
      const fee = new Amount(2000);

      // Initialize the TransactionBuilder
      let builder = new TransactionBuilder();

      // Get the account executing the transaction
      const account = await signer.getAccount();

      // Specify that the fee will be paid from the account
      builder = builder.feeTransactionPayFromComponent(account.address, fee.getStringValue());

      // Template address for creating a new component
      const templateAddress = "resource_217d58767480fbaa48693e11de34baf7fd727e33ee7627f0c4e5b78def378e57"; 

      // Call the template function to create a new component
      builder = builder.callFunction(
        {
          templateAddress,
          functionName: "new",  // Function to call for creating the new component
        },
        []  // Parameters to pass to the function
      );

      // Optionally, add a fee instruction (if needed)
      builder = builder.addFeeInstruction({
        CallMethod: {
          component_address: account.address,  // Fee is paid from the account
          method: "pay_fee",  // Method to pay the fee
          args: [fee.getStringValue()],  // The fee amount
        },
      });

      // Build the transaction
      const transaction = builder.build();

      // Build the transaction request
      const isDryRun = false;  // Set to false to execute the transaction
      const network = Network.LocalNet;  // Network to execute the transaction on
      const requiredSubstates = [];  // No specific substates required

      const submitTransactionRequest = buildTransactionRequest(
        transaction,
        account.account_id,
        requiredSubstates,
        undefined,  // Obsolete inputRefs
        isDryRun,
        network
      );

      // Submit the transaction and wait for the result
      const txResult = await submitAndWaitForTransaction(provider, submitTransactionRequest);
      setTxResult(txResult);  // Save the transaction result
      setIsSubmitting(false); // Reset the submitting state

    } catch (error) {
      console.error("Transaction error:", error);
      setErrorMessage("Failed to submit the transaction.");
      setIsSubmitting(false);
    }
  };

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
        <button onClick={connectToWallet} className="connect-button">
          Connect to Wallet
        </button>
        <p>
          {isConnected ? (
            <span style={{ color: 'green' }}>Wallet is connected!</span>
          ) : (
            <span style={{ color: 'red' }}>Wallet is not connected</span>
          )}
        </p>

        {accountAddress && (
          <p>Account Address: {accountAddress}</p>
        )}

        {errorMessage && (
          <p style={{ color: 'red' }}>{errorMessage}</p>
        )}

        {/* Transaction Submit Button */}
        <button onClick={createAndSubmitTransaction} disabled={isSubmitting} className="submit-button">
          {isSubmitting ? "Submitting..." : "Create and Submit Transaction"}
        </button>

        {/* Display Transaction Result */}
        {txResult && (
          <div>
            <h3>Transaction Result:</h3>
            <pre>{JSON.stringify(txResult, null, 2)}</pre>
          </div>
        )}
      </div>

      <p className="read-the-docs">
        Click on the Vite and React logos to learn more
      </p>
    </>
  );
}

export default App;
```

### Explanation of Each Addition:

1. **State Management for Transaction:**
   ```tsx
   const [isSubmitting, setIsSubmitting] = useState(false);  // Track submission state
   const [txResult, setTxResult] = useState<any>(null); // Store the result of the transaction
   ```
   These states are used to track the submission process and to store the result of the transaction once it is completed.

2. **Transaction Logic:**
   ```tsx
   const createAndSubmitTransaction = async () => {
     if (!accountAddress) {
       setErrorMessage("Account address is not available.");
       return;
     }

     setIsSubmitting(true);  // Start the submission process
     setErrorMessage(null);  // Clear previous error message
   ```
   - This function builds a transaction using the `TransactionBuilder` from the `@tari-project/tarijs` library.
   - It checks if an account is connected before proceeding and sets the `isSubmitting` flag to `true` while the transaction is being processed.

3. **Building the Transaction:**
   ```tsx
   let builder = new TransactionBuilder();
   const fee = new Amount(2000);  // Define a fee for the transaction
   builder = builder.feeTransactionPayFromComponent(account.address, fee.getStringValue());
   ```
   - The fee is set as an `Amount` object with 2000 units.
   - The fee is attached to the transaction using `feeTransactionPayFromComponent`.

4. **Creating a New Component:**
   ```tsx
   builder = builder.callFunction(
     { templateAddress, functionName: "new" }, [1, 2, 3]  // Parameters for the new component
   );
   ```
   - This part defines a call to a template function (`"new"`) to create a new component on the Tari network.

5. **Optional Fee Instruction:**
   ```tsx
   builder = builder.addFeeInstruction({
     CallMethod: {
       component_address: account.address,
       method: "pay_fee",
       args: [fee.getStringValue()],
     },
   });
   ```
   - This optional step adds a fee instruction explicitly, if needed, for the transaction.

6. **Transaction Request:**
   ```tsx
   const submitTransactionRequest = buildTransactionRequest(
     transaction,
     account.account_id,
     requiredSubstates,
     undefined,
     isDryRun,
     network
   );
   ```
   - The `buildTransactionRequest` function is used to construct the transaction request with the specified parameters (e.g., network, dry run, etc.).

7. **Submitting the Transaction:**
   ```tsx
   const txResult = await submitAndWaitForTransaction(provider, submitTransactionRequest);
   setTxResult(txResult);  // Save the transaction result
   setIsSubmitting(false); // Reset the submission state
   ```
   - Finally, the transaction is submitted and the result is displayed once the transaction is confirmed.

### User Interface:
- A button is added to allow the user to create and submit the transaction.
- If the transaction is in progress, the button shows "Submitting...".
- Once the transaction completes, the result is displayed in JSON format.

By following these changes, you can now handle wallet connections, submit transactions with fees, and display the results in the React app!