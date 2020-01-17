# How to Use

With our pallet now compiling and passing it's tests, we're ready to add it to our node.

We first add our newly-created crate as a dependency in the node's runtime Cargo.toml. Then we tell the pallet to only build its std feature when the runtime itself does, as follows:

`my-node/runtime/Cargo.toml`

``` TOML
# --snip--

[dependencies.pallet-did]
default-features = false
path = 'https://github.com/substrate-developer-hub/pallet-did'
package = 'pallet-did'
rev = '92cd623a7186065937744eb24533d9acc609221b'
version = '2.0.0'


# toward the bottom
[features]
default = ['std']
std = [
    'pallet-did/std',
    # --snip--
]
```
Next we will update `my-node/runtime/src/lib.rs` to actually use our new runtime pallet, by adding a trait implementation with our pallet_did and add it in our construct_runtime! macro.

``` rust
// add the following code block
impl pallet_did::Trait for Runtime {
  type Event = Event;
  type Public = AccountId;
  type Signature = Signature;

}

// --snip--
construct_runtime!(
  pub enum Runtime where
    Block = Block,
    NodeBlock = opaque::Block,
    UncheckedExtrinsic = UncheckedExtrinsic
  {
    // --snip--
    // add the following line
    PalletDID: pallet_did::{Module, Call, Storage, Event<T>},
  }
);
```

Follow the [Creating an External Pallet](https://substrate.dev/docs/en/tutorials/creating-a-runtime-module) to get a more detailed explanation on how to integrate a pallet into your node.

## Building and Testing

Before you release your pallet, you should check that it can:

1. Build to Native:

    ```
    cargo build --release
    ```

2. Pass your tests:

    ```
    cargo test
    ```