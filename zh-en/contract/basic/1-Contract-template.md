# Contract Template
Let's take a look at a high level what is available to you when developing a smart contract using the ink!.

## ink！

ink! is an eDSL to write WebAssembly based smart contracts in the Rust programming language.

ink! is just standard Rust in a well defined "contract format" with specialized #[ink(...)] attribute macros. These attribute macros tell ink! what the different parts of your Rust smart contract represent, and ultimately allows ink! to do all the magic needed to create Substrate compatible Wasm bytecodes.


## Create a contract template

* Docker

```bash
docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract new flipper
```

* Local

```bash
cargo contract new flipper

```

After the command is executed, you will get the following directory:

```bash
flipper
  .ink
  lib.rs
  Cargo.toml
```
Cargo.toml is the project dependent configuration, and the contract logic is in the lib.rs file:

## Smart contract architecture

Constructing an ink smart contract is similar to the solidity smart contract, in which the main components of solidity we expect are also consistent in the ink: contract variables, events, public functions and private functions, and environment variables that obtain the address of the caller.

The following is Erc20 contract architecture ：

```rust
// import ink module
use ink_lang as ink;

mod erc20{
    // declare modules
    use ink_core::{
        env::{chainx_calls, chainx_types::Call, DefaultXrmlTypes},
        storage,
    };
    ...

    //define events
    #[ink(event)]
    struct Transfer {
        ...
    }

    // contract variables as a struct
    struct erc20 {
      owner: storage::Value<AccountId>,
      ...
    }

    // compulsory deploy method that is run upon the initial contract instantiation
    fn new(&mut self, init_value: u64){}
    
    // public contract methods in an impl{} block with macro #[ink(message)]
    impl NFToken {
      #[ink(message)]
      fn total_supply(&self) -> u64 {}
      // private contract methods without macro #[ink(message)]
      fn transfer_impl() ->bool {}
      ...
   }
}

```
