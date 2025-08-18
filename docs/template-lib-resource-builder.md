# `ResourceBuilder` Module

The `ResourceBuilder` module allow developers to create resources on the Ootle using the `fungible`, `non-fungible` and `confidential` builders. The builders allow you to create the resource.

# Construction

Creates 
```rust
 let initial_tokens = ResourceBuilder::confidential()
                .with_metadata(token_metadata.clone())
                .with_token_symbol(&token_symbol)
                // Access rules
                .mintable(require_admin.clone())
                .burnable(require_admin.clone())
                .depositable(require_user.clone())
                .withdrawable(require_user.clone())
                .recallable(require_admin.clone())
                .with_authorization_hook(component_alloc.get_address(), "authorize_user_deposit")
                .with_view_key(view_key)
                .initial_supply(initial_supply_proof);
```



/// ```ignore
/// use tari_template_lib::resource::builder::ResourceBuilder;
///
/// let resource_address = ResourceBuilder::fungible()
///     .with_token_symbol("TARI")
///     .with_divisibility(9)
///     .mintable(rule!(allow_all))
///     .build();
/// ```