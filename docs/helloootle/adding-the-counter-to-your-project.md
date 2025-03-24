# Introduction

We'll be modifying your existing project to add a "Create Counter" button with ability to create a new Counter on the Ootle. In order to do this, we'll need to add the following:

- Import some of the pre-existing functions for transactions from the TariProvider in tari.js
- Add variables to track the submission and result of the transaction
- Add a function to our app to call the Transaction Builder
- Build a transaction with various required data, such the fees and the template address.
- Create a new counter and report the location of the counter on the Ootle

Let's go through the steps.

## Import the Functions
```typescript linenums="3"
    import { Amount, TransactionBuilder, buildTransactionRequest, Network, submitAndWaitForTransaction } from "@tari-project/tarijs";
```

This imports the necessary functions from Tari.js in order to build a transaction, submit it to the network and await the results.

## State Management for Transaction:**
```typescript linenums="11"
    const [isSubmitting, setIsSubmitting] = useState(false);  // Track submission state
    const [txResult, setTxResult] = useState<any>(null); // Store the result of the transaction
```

These states are used to track the submission process and to store the result of the transaction once it is completed.

## Starting the Builder
   ```tsx linenums="30"
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

## Setting our variables for our transaction submission
```tsx 
    try {
      // Create the fee amount 
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



