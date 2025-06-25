# Introduction

We'll be modifying your existing project to add a "Create Counter" button with ability to create a new Counter on the Ootle. In order to do this, we'll need to add the following:

- Import some of the pre-existing functions for transactions from the TariProvider in tari.js
- Add variables to track the submission and result of the transaction
- Add a function to our app to call the Transaction Builder
- Build a transaction with various required data, such the fees and the template address.
- Create a new counter and report the location of the counter on the Ootle

Let's go through the steps. You can also find the fully amended code at the end of this lesson.

## Import the Functions
```typescript linenums="3"
    import { Amount, TransactionBuilder, buildTransactionRequest, Network, submitAndWaitForTransaction } from "@tari-project/tarijs";
```

This imports the necessary functions from Tari.js in order to build a transaction, submit it to the network and await the results.

## State Management for Transaction
```typescript linenums="11"
    const [isSubmitting, setIsSubmitting] = useState(false);  // Track submission state
    const [txResult, setTxResult] = useState<any>(null); // Store the result of the transaction
    const [signer, setSigner] = useState<any>(null); 
```

These states are used to track the submission process and to store the result of the transaction once it is completed.

## Adding the Transaction Builder Function
### Starting the Builder
   ```typescript linenums="35"
   const createAndSubmitTransaction = async () => {
     if (!accountAddress) {
       setErrorMessage("Account address is not available.");
       return;
     }

     setIsSubmitting(true);  // Start the submission process
     setErrorMessage(null);  // Clear previous error message
   ```

This function builds a transaction using the `TransactionBuilder` from the `@tari-project/tarijs` library.
It checks if an account is connected before proceeding and sets the `isSubmitting` flag to `true` while the transaction is being processed.

### Setting our variables for our transaction submission
```typescript linenums="44"
    try {
      // Create the fee amount.
      const fee = new Amount(20000);

      // Initialize the TransactionBuilder
      let builder = new TransactionBuilder();

      // Get the account executing the transaction
      const account = await signer.getAccount();

      // Specify that the fee will be paid from the account
      builder = builder.feeTransactionPayFromComponent(account.address, fee.getStringValue());

      // Template address for creating a new component
      const templateAddress = "resource_217d58767480fbaa48693e11de34baf7fd727e33ee7627f0c4e5b78def378e57"; 
```

here we define a number of the required variables we will be using in the course of submitting the transaction. The two that are of particular note here is creating a new `TransactionBuilder` and the template address, which is where the counter template is located.

### Creating a New Component
```typescript
   builder = builder.callFunction(
     { templateAddress, functionName: "new" },
     []  // Parameters for the new component
   );
```
This part defines a call to a template function (`"new"`) to create a new component on the Tari network.

### Optional Fee Instruction
```typescript
   builder = builder.addFeeInstruction({
     CallMethod: {
       component_address: account.address,
       method: "pay_fee",
       args: [fee.getStringValue()],
     },
   });
```

This step adds a fee instruction explicitly for the transaction. Every transaction on the Ootle requires fees to be processed.

### Transaction Request
```typescript
   const submitTransactionRequest = buildTransactionRequest(
     transaction,
     account.account_id,
     requiredSubstates,
     undefined,
     isDryRun,
     network
   );
```
The `buildTransactionRequest` function is used to construct the transaction request with the specified parameters (e.g., network, dry run, etc.).

### Submitting the Transaction
```typescript
   const txResult = await submitAndWaitForTransaction(provider, submitTransactionRequest);
   setTxResult(txResult);  // Save the transaction result
   setIsSubmitting(false); // Reset the submission state
```
Finally, the transaction is submitted and the result is displayed once the transaction is confirmed.

## Adding the UI

We'll also need to add a way to call this counter. Let's add the following in our `return()` section:

```typescript
        {/* Transaction Submit Button */}
        <button onClick={createAndSubmitTransaction} disabled={isSubmitting} className="submit-button">
          {isSubmitting ? "Submitting..." : "Create Counter"}
        </button>

        {/* Display Transaction Result */}
        {txResult && (
          <div>
            <h3>Transaction Result:</h3>
            <p>Counter Created</p>
            <p>Component Address: {txResult.result?.component_address || "Unknown Address"}</p>
          </div>
        )}
      </div>
```

This adds a button called "Create Counter" and, when pressed, will build the transaction and subsequently report if the transaction had been successfully completed with the component address.

This will create a `component` substate on the Ootle - this means that you will have a Counter instance that is independent of the `template` that can be interacted with via specific functions.

Next, we'll look at using transactions to interact with this `component` to increase the counter amount.
