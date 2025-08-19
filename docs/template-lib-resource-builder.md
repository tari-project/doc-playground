# `ResourceBuilder` Module

The `ResourceBuilder` module allow developers to create resources on the Ootle. There are four builder methods available

| Method | What it does | When to use  |
| ---------------- | ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `fungible()`     | Creates a **publicly visible fungible resource**. Balances are stored in vaults and visible to anyone with ledger access.  | Use when transparency is acceptable or required (e.g., utility tokens, in-game currencies). |
| `non_fungible()` | Creates a **publicly visible non-fungible resource**. Each token has a unique ID and is visible in vaults.  | Use for unique items like collectibles, digital art, identity or governance badges.|
| `confidential()` | Creates a **partially confidential fungible resource**. Balances are split into **revealed** and **blinded outputs**; requires special viewing mechanisms for full balance visibility. | Use when partial privacy is needed. Revealed funds can be used for fees or refunds, while blinded outputs preserve privacy. |
| `stealth()`      | Creates a **fully confidential fungible resource**. Does **not use vaults**; each unit exists as a **confidential substate** on the ledger. | Use for maximum privacy. Ideal when you need transaction-level confidentiality without vault visibility.|

## Construction

Initializing a new resource can be done in two ways:

| Method | What it does | When to use |
|--------|--------------|-------------|
|`.build()` | Creates resource definition only	| When you want to mint tokens/NFTs later or on-demand |
|`.initial_supply()` | Creates resource and mints supply | When you want to mint and distribute tokens/NFTs immediately |

You may also chain various configuration methods in the builder, such as the token details or minting rules.

> Note: user either `.build()` or `.initial_supply()`, not both.

**Example 1**

Create a new fungible token, immediately mints an initial set of tokens and returns a `Bucket` containing the tokens.

```rust
// Create a fungible token called "DemoToken" with symbol "DMT"
let initial_supply_bucket = ResourceBuilder::fungible()
    .with_metadata("name", "DemoToken")
    .with_metadata("description", "A demo fungible token with admin-only minting and burning.")
    .with_token_symbol("DMT")
    // Only addresses presenting the admin badge can mint new tokens
    .mintable(rule!(require(admin_badge_address)))
    // Only addresses presenting the admin badge can burn tokens
    .burnable(rule!(require(admin_badge_address)))
    // Anyone can deposit tokens to vaults or accounts (using rule!(allow_all) directly)
    .depositable(rule!(allow_all))
    // Anyone can withdraw tokens from vaults or accounts (using rule!(allow_all) directly)
    .withdrawable(rule!(allow_all))
    // Mint an initial supply of 10,000 tokens and return a Bucket containing them
    .initial_supply(dec!(10_000));
```

### `.build()`

Builds a `Resource` definition and returns a `ResourceAddress` which can then be used as a reference for the `ResourceManager`.

**Example**
```rust
// Set up the resource with metadata, divisibility, and access rules as needed
let resource_address = ResourceBuilder::fungible()
    .with_token_symbol("MY_TOKEN")
    .with_divisibility(8)
    .mintable(rule!(allow_all)) // Allows anyone to mint new tokens
    .build(); // This creates the resource and returns its address
```

### `initial_supply`

Builds a resource internally and then mints a number of the resources as an initial supply. Depending on the builder method, the argument required for `.initial_supply` will differ.

| Builder Method   | `initial_supply` Signature                                                 | Input Type                                                          | Description                                                                                    |
| ---------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `fungible()`     | `initial_supply<A: Into<Amount>>(initial_supply: A)`                       | `Amount` or compatible type (e.g. `u64`, `Decimal`)                 | Mints a **fungible resource** with the specified amount. Publicly visible.                     |
| `non_fungible()` | `initial_supply<I: IntoIterator<Item = NonFungibleId>>(initial_supply: I)` | Iterator of `NonFungibleId`                                         | Mints **NFTs** with null immutable and mutable data.                                           |
| `non_fungible()` | `initial_supply_with_data<I, T, U>(initial_supply: I)`                     | Iterator of `(NonFungibleId, (&T, &U))` where `T`, `U`: `Serialize` | Mints **NFTs** with **custom immutable and mutable data**.                                     |
| `confidential()` | `initial_supply(initial_supply_proof: ConfidentialOutputStatement)`        | `ConfidentialOutputStatement`                                       | Mints a **confidential fungible resource** using a blinded output proof. Partially private.    |
| `stealth()`      | `initial_supply(initial_supply: StealthMintStatement)`                     | `StealthMintStatement`                                              | Mints a **stealth resource**, fully confidential. Returns a `ResourceAddress`, not a `Bucket`. |

> Note: the `ConfidentialOutputStatement` and `StealthMintStatement` cannot be generated within the template. Generating the `ConfidentialOutputStatement` is non-trivial, and requires use of several cryptographic libraries and techniques. While we do not cover its construction in this documentation, examples are available in the test suite for the Tari templates, located in the official repo under [https://github.com/tari-project/tari-ootle/blob/development/crates/engine/tests/](https://github.com/tari-project/tari-ootle/blob/development/crates/engine/tests/). You can review the tests in the [`confidential.rs`](https://github.com/tari-project/tari-ootle/blob/development/crates/engine/tests/confidential.rs) and [`stealth.rs`](https://github.com/tari-project/tari-ootle/blob/development/crates/engine/tests/stealth.rs) files respectively.