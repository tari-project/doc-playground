# `ResourceManager` Module

This module provides the `ResourceManager` struct, a high-level interface for managing Tari Ootle resources. Resources can be non-private fungible tokens, non-fungible tokens, confidential fungible, and stealth fungible.

It abstracts common operations like creating resources, minting tokens, recalling tokens from vaults, querying supply, and updating non-fungible metadata.

The `ResourceManager` uses engine calls to perform resource operations and enforces access rules and permissions based on the resource configuration.

## Examples

```rust,ignore
use tari_template_lib::prelude::*;

// Assume `my_resource_address` is a ResourceAddress

let resource_manager = ResourceManager::get(my_resource_address);

// Mint 1000 units of the fungible resource
let bucket = resource_manager.mint_fungible(1000);

// (Optional) Deposit into a Vault
self.vault.deposit(bucket);
```

--

## Notes

- All mint/burn/update calls check the appropriate `ResourceAccessRules` before proceeding.
- When a minting function returns a `Bucket`, it contains the newly minted resource(s).


# `ResourceManager` Struct

```rust
#[derive(Debug, Serialize, Deserialize)]
#[serde(transparent)]
pub struct ResourceManager {
    resource_address: ResourceAddress,
}
```

Provides an interface for various resource operations e.g. Minting, recalling, creating, etc.

## Example

```rust,ignore
use tari_template_lib::resource::manager::ResourceManager;
let resource_manager = ResourceManager::get(my_resource_address);
resource_manager.mint_fungible(Amount(1000));
```

# Methods

## `get`

```rust
pub fn get(resource_address: ResourceAddress) -> Self
```

Returns the address of the resource that is being managed.

## `resource_address`

```rust
pub fn resource_address(&self) -> ResourceAddress
```

Returns the address of the resource that is being managed.

## `resource_type`

```rust
pub fn resource_type(&self) -> ResourceType
```

A public function that returns the resource type of the resource being managed.

**Panics:**

If the resource type is not recognized on a resource or if the resource address is not set via ResourceManager.

## `create`

```rust
pub(crate) fn create(
    resource_type: ResourceType,
    owner_rule: OwnerRule,
    access_rules: ResourceAccessRules,
    metadata: Metadata,
    mint_arg: Option<MintArg>,
    view_key: Option<RistrettoPublicKeyBytes>,
    authorize_hook: Option<AuthHook>,
    address_allocation: Option<ResourceAddressAllocation>,
    divisibility: u8,
    is_total_supply_tracking_enabled: bool,
) -> (ResourceAddress, Option<Bucket>)
```

Creates a new resource on the Tari network.

This function registers a new resource, such as a fungible, non-fungible, or confidential asset, and optionally mints an initial supply. It returns the address of the created resource and, if tokens were minted, a Bucket containing them.

**Note:**  
The `create` method is `pub(crate)` and is typically only called internally by the engine or advanced template code. Most users create resources via higher-level builder APIs.

**Arguments:**

* `resource_type` – The type of resource to create, defined by the \[ResourceType] enum.
* `owner_rule` – Specifies \[OwnerRule]s, such as requiring a signature or badge to control the resource.
* `access_rules` – Defines fine-grained permissions (\[ResourceAccessRules]) for actions like minting, burning, or updating data.
* `metadata` – Immutable metadata that describes the resource, such as name, symbol, or description.
* `mint_arg` – Optional initial minting configuration. Must match the resource\_type.
* `view_key` – (Optional) A \[RistrettoPublicKeyBytes] used for confidential assets to enable visibility control.
* `authorize_hook` – (Optional) An \[AuthHook] for delegating authorization to another component.
* `address_allocation` – (Optional) A specific \[ResourceAddressAllocation] used to predefine the address.
* `divisibility` – Number of decimal places allowed. For non-fungible resources, must be 0.
* `is_total_supply_tracking_enabled` – Whether total supply should be tracked and queryable.

**Returns:**

A tuple of:

* \[ResourceAddress] – The address of the newly created resource.
* \[Option<Bucket>] – A \[Bucket] containing the initial minted tokens, if any.

**Panics:**

Panics if:

* resource\_type does not match mint\_arg
* divisibility is non-zero for a non-fungible resource

**Example:**

```rust,ignore
use tari_template_lib::{
    models::{Amount, Metadata, ResourceAccessRules, ResourceType, OwnerRule, MintArg},
    prelude::*,
};

let access_rules = ResourceAccessRules::default(); // all actions denied by default
let metadata = Metadata::from_iter([("name", "MyToken"), ("symbol", "MTK")]);

let (address, bucket) = resource_manager.create(
    ResourceType::Fungible,
    OwnerRule::None,
    access_rules,
    metadata,
    Some(MintArg::Fungible { amount: Amount(1_000_000) }),
    None,
    None,
    None,
    6, // divisible to 6 decimal places
    true,
);
```

## `mint_confidential`

```rust
pub fn mint_confidential(&self, statement: ConfidentialOutputStatement) -> Bucket
```

Mints new tokens for the confidential resource managed by this ResourceManager.

This method accepts a zero-knowledge proof that authorizes the minting of confidential tokens. Upon success, a \[Bucket] containing the newly minted tokens is returned to the caller.

**Arguments:**

* `statement` – A \[ConfidentialOutputStatement] containing the zero-knowledge statement and associated metadata. This includes the output and change statements, a range statement, and revealed amounts for output and change.

**Returns:**

A \[Bucket] containing the newly minted confidential tokens.

**Panics:**

This method will panic if:

* The resource is not of type \[ResourceType::Confidential]
* The provided statement is invalid or malformed
* The caller lacks the required minting permissions, as defined by the resource's \[ResourceAccessRules]

**Example:**

```rust,ignore
let bucket = resource_manager.mint_confidential(statement);
// The returned Bucket contains the newly minted confidential tokens.
```

## `mint_stealth`

```rust
pub fn mint_stealth(&self, statement: StealthMintStatement)
```

Mints new tokens for the stealth resource managed by this ResourceManager.

This method accepts a zero-knowledge proof that authorizes the minting of stealth tokens.

**Arguments:**

* `statement` – A \[StealthMintStatement] containing the outputs to mint, and a range proof.

**Panics:**

This method will panic if:

* The resource is not of type \[ResourceType::Confidential]
* The provided statement is invalid or malformed
* The caller lacks the required minting permissions, as defined by the resource's \[ResourceAccessRules]

**Example:**

```rust,ignore
let bucket = resource_manager.mint_stealth(statement);
// The returned Bucket contains the newly minted stealth tokens.
```

## `mint_non_fungible`

```rust
pub fn mint_non_fungible<T: Serialize, U: Serialize>(
    &self,
    id: NonFungibleId,
    metadata: &T,
    mutable_data: &U,
) -> Bucket
```

Mints a new non-fungible token (NFT) for the resource managed by this ResourceManager.

This method creates an NFT with the specified \[NonFungibleId] and attaches associated metadata and mutable data. The data must be serializable. On success, a \[Bucket] containing the newly minted NFT is returned.

**Type Parameters:**

* `T` – The type of the static (immutable) metadata associated with the NFT.
* `U` – The type of the mutable data that can later be updated, subject to access rules.

**Arguments:**

* `id` – A unique identifier for the NFT. Must be one of the supported \[NonFungibleId] variants: U256, Uint64, Uint32, or String.
* `metadata` – Immutable data describing the NFT (e.g., name, attributes, external links).
* `mutable_data` – Data that can be updated after minting (e.g., usage counters or evolving states).

**Returns:**

A \[Bucket] containing the newly minted non-fungible token.

**Panics:**

This method will panic if:

* The resource is not of type \[ResourceType::NonFungible]
* Serialization of the metadata or mutable data fails
* The caller does not have minting permissions as defined by \[ResourceAccessRules]

**Example:**

```rust,ignore
let nft = resource_manager.mint_non_fungible(
    NonFungibleId::String("unique_nft_id".to_string()),
    &MyMetadata { name: "My NFT".into() },
    &MyMutableData { views: 0 },
);
// The returned Bucket contains the newly minted NFT.
```

## `mint_many_non_fungible`

```rust
pub fn mint_many_non_fungible<T: Serialize + ?Sized, U: Serialize + ?Sized>(
    &self,
    metadata: &T,
    mutable_data: &U,
    supply: u32,
) -> Bucket
```

Mints multiple non-fungible tokens of the resource being managed, each with the same metadata and mutable data. Returns a \[Bucket] containing the newly minted tokens.

This method generates a number of unique \[NonFungibleId]s using cryptographically secure random identifiers. The supply parameter controls how many tokens are minted.

**Note:**

This method generates random \[NonFungibleId]s locally without checking for global uniqueness. While collisions are extremely unlikely due to the large ID space, they are theoretically possible. Consumers should be aware of this when minting large numbers of NFTs.

**Arguments:**

* `metadata` - A serializable value representing the immutable metadata of the non-fungibles. This is typically static information that does not change over the token's lifetime (e.g., name, image URL, category).
* `mutable_data` - A serializable value representing data that can be updated after minting (e.g., usage stats, upgrade level).
* `supply` - The number of non-fungible tokens to mint. Each token will have a unique, randomly generated \[NonFungibleId].

**Access Control:**

The caller must satisfy the resource's mintable access rule defined in the \[ResourceAccessRules].

**Panics:**

This function will panic if the resource being managed is not of type \[ResourceType::NonFungible] or if serialization of metadata or mutable\_data fails.

**Example:**

```rust,ignore
let bucket = manager.mint_many_non_fungible(
    &MyMetadata { name: "Gem".into() },
    &MyMutableData { durability: 100 },
    10,
);
// The returned Bucket contains the newly minted NFTs.
```

