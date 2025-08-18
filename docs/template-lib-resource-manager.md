# ResourceManager

The `ResourceManager` provides an interface for interacting with resources in the Tari network. It allows developers to **mint**, **recall**, **query**, and **update** resources after they have been created.

## Construction

### `ResourceManager::get(address: ResourceAddress) -> ResourceManager`

Creates a new `ResourceManager` bound to an existing resource.

```rust
let resource_manager = ResourceManager::get(my_resource_address);
```

---

## Introspection

### `resource_type() -> ResourceType`

Returns the [`ResourceType`](./resource-types.html) of the managed resource.

This tells you whether the resource is:

* `Fungible`
* `NonFungible`
* `Confidential`

---

### `total_supply() -> Amount`

Returns the total supply of the resource, **only if** total supply tracking is enabled.
Panics otherwise.

---

### `total_supply_opt() -> Option<Amount>`

Like `total_supply()`, but returns `None` if total supply tracking is not enabled.

---

## Minting

Minting refers to **creating new tokens** of the managed resource. Access rules must permit this action.

### `mint_fungible(amount: Amount) -> Bucket`

Mints the given amount of **fungible tokens**.
Panics if the resource is not fungible.

---

### `mint_non_fungible(id, metadata, mutable_data) -> Bucket`

Mints a single **non-fungible token** (NFT) with the given ID, metadata, and mutable data.

* `id` must be unique
* Panics if resource is not non-fungible or ID already exists

---

### `mint_many_non_fungible(metadata, mutable_data, supply: u32) -> Bucket`

Mints multiple NFTs with randomly generated IDs.

---

### `mint_many_non_fungible_with(metadata, mutable_data, producer) -> Bucket`

Advanced minting API. Uses a **custom ID producer** function to determine each token's ID.

* Stops minting when `producer()` returns `None`
* Panics if a duplicate ID is returned

---

### `mint_confidential(proof: ConfidentialOutputStatement) -> Bucket`

Mints **confidential tokens** using a valid zero-knowledge proof.
Panics if:

* The resource is not confidential
* Proof is invalid
* Minting is not authorized

---

## Recalling Tokens

"Recalling" allows forcibly withdrawing tokens from a vault. This requires proper authorization (via access rules).

### `recall_fungible_all(vault_id: VaultId) -> Bucket`

Recalls **all fungible tokens** from the given vault.

---

### `recall_fungible_amount(vault_id, amount) -> Bucket`

Recalls a specific amount of fungible tokens.

Panics if:

* Resource is not fungible
* Not authorized

---

### `recall_non_fungible(vault_id, token_id) -> Bucket`

Recalls a **single non-fungible token** from the vault.

---

### `recall_non_fungibles(vault_id, token_ids: BTreeSet<NonFungibleId>) -> Bucket`

Recalls **multiple NFTs** from a vault in one operation.

---

### `recall_confidential(vault_id, commitments, revealed_amount) -> Bucket`

Recalls **confidential tokens** from a vault using Pedersen commitments and a revealed amount.

---

## Non-Fungible Operations

These methods are available **only for non-fungible resources**.

### `get_non_fungible(id: &NonFungibleId) -> NonFungible`

Fetches a single NFT by its ID.

Panics if:

* Resource is not non-fungible
* ID does not exist

---

### `update_non_fungible_data(id, data)`

Updates the **mutable data** of an NFT.

Panics if:

* Token doesn't exist
* Unauthorized

---

## Access Rules

### `set_access_rules(access_rules: ResourceAccessRules)`

Updates the access control policies for the resource.

You can modify:

* Who can mint
* Who can recall
* Who can update metadata, etc.

Panics if caller is not authorized to update rules.