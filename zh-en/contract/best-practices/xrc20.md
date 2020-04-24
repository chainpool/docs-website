# ChainX cross-chain asset Xrc20

## Introduction

Currently the ChainX smart contracts only support bitcoins.

The ChainX smart contracts adopt the “local currency + token” model to take multi tokens onboard, because of the similarities between Substrate Contracts’ smart contract platform and Ethereum’s.

In this model, ChainX's inter-chain assets on smart contracts adopt the contract token model, so PCX, like Ethereum provides gas and local currency circulation for smart contracts and inter-chain assets are introduced in the form of contract tokens.

Reasons for adopting token models instead of introducing tokens directly:

1. The Ethereum token contract is relatively mature, so it is easier for contract developers to migrate the Ethereum contract to the ChainX smart contract platform under the token model.
2. If the multi-token concept is introduced, Substrate Contracts will be greatly modified, which easily leads to unexpected troubles.

At present, ChainX’s smart contracts adopt the XRC20 model for bitcoins. According to contract developers’ feedback, other models may be adopted in the future.

1. XRC20 (formerly Ethereum ERC20)
2. XRC721 (formerly Ethereum ERC721)
3. XRC777 (formerly Ethereum ERC777)


### ChainX’s bitcoin X-BTC

Currently X-BTC corresponds to the token model XRC20 used in the smart contract platform. Users are free to convert ChainX's X-BTC to XRC20 in the contract platform and vice versa.

![image-domain](https://user-images.githubusercontent.com/5023721/68760304-7e08ab80-064c-11ea-9c00-28677d41e5d0.png '')

ChainX modified the ERC20 contract standard (XRC20) by adding two interfaces, issue and destroy. In addition to that, when a contract is deployed to the chain, it has to be instantiated with the contract instance uniquely specified, therefore, token instances other than the specified instance are not recognized by ChainX's Runtime.

* Issue can only be called by convert_to_xrc20 in Runtime, which cannot be called by calling the contract directly. After convert_to_xrc20 is called, the user's assets will be transferred to the contract instance, and the corresponding amount will be automatically issued to the account that sent the transaction.
* Destroy can be called by users to transfer the assets in the contract to the ChainX asset module. First, the corresponding tokens in the contract are destroyed, and then the contract will automatically call convert_to_assets to return the assets from the contract to the user, but convert_to_assets cannot be called through an external transaction.

## Contract Implement

Next we'll use ink to implement an Xrc20 project and implement all the interfaces for Xrc20.

### Create Smart Contract

Run `docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract new xrc20` to create a new project。


#### Module 

```rust
use ink_lang as ink;
```

You'll also notice that before the module declaration, there are the following:

```rust
#![cfg_attr(not(feature = "std"), no_std)]
```
If we use the std feature flag in our code, the line states that we are using the standard library. Otherwise, the contract will always be compiled using no_std. The Ink contract does not use the Rust standard library, so unless we clearly define it, it will be omitted from compilation.

## Module DeFine 

Define xrc20module and import ink_core, ink_prelude, Encode and other modules.Encode events into the original format.

```rust

mod xrc20 {
   use ink_core::{
        env::{chainx_calls, chainx_types::Call, DefaultXrmlTypes},
        storage,
    };
    use ink_prelude::vec::Vec;
    use scale::Encode;
    ...
}
```

### Event Define

Event can also be seen as an important aspect of smart contracts for blockchain notifications, which proactively send out data when something happens, allowing DAPP to react to it in real time.

Therefore, our xrc20 defines four Event events: Transfer, Approve, Issue, And Destroy.

```rust
pub type Text = Vec<u8>;

#[ink(event)]
struct Transfer {
    #[ink(topic)]
    from: Option<AccountId>,
    #[ink(topic)]
    to: Option<AccountId>,
    value: u64,
}

#[ink(event)]
struct Approval {
    owner: AccountId,
    spender: AccountId,
    value: u64,
}

#[ink(event)]
struct Issue {
    to: AccountId,
    value: u64,
}

#[ink(event)]
struct Destroy {
    owner: AccountId,
    value: u64,
}

```
AccountId type by ink! Core provides the accountID for an account (equivalent to the address type of Ethereum). Another type available is balance, which is the u64 type, or 64-bit unsigned integer.

### Smart Contract variable

Smart contract variables can be considered class properties accessed within a function through self. Here are the contract variables for our Xrc20 contract:

```rust
struct Xrc20 {
    /// The total supply.
    total_supply: storage::Value<u64>,
    /// The Decimals of this token.
    /// We choose u16 instead of u8 here in that the parity_codec 3.5 is lack of u8 support.
    decimals: storage::Value<u16>,
    /// The Name of the token.
    name: storage::Value<Text>,
    /// The Symbol of the token.
    symbol: storage::Value<Text>,
    /// The balance of each user.
    balances: storage::HashMap<AccountId, u64>,
     /// u64s that are spendable by non-owners: (owner, spender) -> allowed
    allowances: storage::HashMap<(AccountId, AccountId), u64>,
}
```
The first two variables are of the type of storage::Value, and the following three storages::HashMap. In fact, ink_core storage module must be used for any contract data that we want to have a lasting presence on the chain.
The storage type is generic, so we explicitly provide the type of data we store in angle brackets.
By defining the required contract data, let's explore some contract implementations and highlight some key logic and syntax.

### Contract Deploy

The deployment function is mandatory in any Ink smart contract and is called when deployed to a chain to instantiate a smart contract.

```rust

impl Xrc20 {
    #[ink(constructor)]
    fn new(&mut self, init_value: u64, name: Text, symbol: Text, decimals: u16) {
        assert!(self.env().caller() != AccountId::from([0; 32]));
        self.total_supply.set(init_value);
        self.name.set(name);
        self.symbol.set(symbol);
        self.decimals.set(decimals);
        self.balances.insert(self.env().caller(), init_value);
        self.env().emit_event(Transfer {
            from: None,
            to: Some(self.env().caller()),
            value: init_value,
        });
    }
    ...
}
```

### Transfer Token

The transfer of tokens is arguably the most important feature of our contract, allowing us to transfer tokens to and from accounts. '#[ink(message)]'. The function is of type public, and the public transfer() function can be used to send the transfer request and has the following signature:

```rust
 #[ink(message)]
fn transfer(
    &mut self, 
    to: AccountId, value: u64) -> bool {
}

```
This function calls the private transfer_impl () function to perform the actual transfer logic. Ensure that the following conditions are met for transfer:

1. We immediately check to see if the caller is the token owner, and if not, return false

2. Update the id_to_token map to override the value of the token_id index to the new owner's account

3. Update the token count to reduce the sender count and increase the receiver count

4. The Transfer event is issued through emit_event()

Arguably, this is a simpler function than the build process, and this transport implementation allows tokens to be sent separately. The basic mechanism here is simply to update id_to_owner mapping and track who owns what. From here, the sender and recipient owner_to_token_count records are also updated to track the tokens that the personal account owns.

```rust

/// Transfers token from a specified AccountId to another AccountId.
fn transfer_impl(&mut self, from: AccountId, to: AccountId, value: u64) -> bool {
    assert!(from != AccountId::from([0; 32]));
    assert!(to != AccountId::from([0; 32]));

    let balance_from = self.balance_of_or_zero(&from);
    let balance_to = self.balance_of_or_zero(&to);

    let previous_balances = balance_from + balance_to;

    if balance_from < value {
        return false;
    }
    // balance_from - value
    let new_balance_from = match balance_from.checked_sub(value) {
        Some(b) => b,
        None => return false,
    };
    // balance_to + value
    let new_balance_to = match balance_to.checked_add(value) {
        Some(b) => b,
        None => return false,
    };

    // not same to solidity, we set value in the end. thus we do more check before
    // other logic
    if from == to || value == 0 {
        // before set storage
        self.env().emit_event(Transfer {
            from: Some(from),
            to: Some(to),
            value,
        });
        return true;
    }

    // set to storage
    self.balances.insert(from, new_balance_from);
    self.balances.insert(to, new_balance_to);

    // ensure total balance is equal to before
    assert_eq!(
        previous_balances,
        self.balance_of_or_zero(&from) + self.balance_of_or_zero(&to)
    );

    self.env().emit_event(Transfer {
        from: Some(from),
        to: Some(to),
        value,
    });
    true
}

```

### Approve

Approving another account to spend your funds is the first step in the third party transfer process. A token owner can specify another account and any arbitrary number of tokens it can spend on the owner's behalf. The owner need not have all their funds approved to be spent by others; in the situation where there is not enough funds, the approved account can spend up to the approved amount from the owner's balance.

When an account calls approve multiple times, the approved value simply overwrites any existing value that was approved in the past. By default, the approved value between any two accounts is 0, and a user can always call approve for 0 to revoke access to their funds from another account.

To store approvals in our contract, we need to use a slightly fancy HashMap.

Since each account can have a different amount approved for any other account to use, we need to use a tuple as our key which simply points to a balance value. Here is an example of what that would look like:

```rust

struct xrc20{
   allowances: storage::HashMap<(AccountId, AccountId), u64>
}

```
The public approvals() function accepts two parameters when invoked:

* The AccountId we want to approve
* We are adding the approved token_id

The Approvals() signatures are as follows:

```rust
#[ink(message)]
fn approve(&mut self, spender: AccountId, value: u64) -> bool {}

```

When you call the approve function, you simply insert the value specified into storage. The owner is always the self.env().caller(), ensuring that the function call is always authorized.

```rust
#[ink(message)]
fn approve(&mut self, spender: AccountId, value: u64) -> bool {
    let owner = self.env().caller();
    self.allowances.insert((owner, spender), value);
    self.env().emit_event(Approval {
        owner,
        spender,
        value,
    });
    true
}

```
#### Transfer From

Finally, once we have set up an approval for one account to spend on-behalf-of another, we need to create a special transfer_from function which enables an approved user to transfer those funds.

As mentioned earlier, we will take advantage of the private transfer_from_to function to do the bulk of our transfer logic. All we need to introduce is the authorization logic again.

So what does it mean to be authorized to call this function?

1. The self.env().caller() must have some allowance to spend funds from the from account.
2. The allowance must not be less than the value trying to be transferred.
In code, that can easily be represented like so:

```rust
let allowance = self.allowance_of_or_zero(&from, &self.env().caller());
if allowance < value {
    return false
}
....
true
```

Again, we exit early and return false if our authorization does not pass.

If everything looks good though, we simply insert the updated allowance into the allowance HashMap (let new_allowance = allowance - value), and call the transfer_from_to between the specified from and to accounts.


### contract build(Docker is recommended)

The compiled wasm is different on different computers, even they are using the same OS and same source file. Using Docker which provides the required environment for building a contract can guarantee you can always get the same wasm file per ink contract.

$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract build
 [1/4] Collecting crate metadata
 [2/4] Building cargo project
    Finished release [optimized] target(s) in 1.08s
 [3/4] Post processing wasm file
 [4/4] Optimizing wasm file
 Original wasm size: 53.2K, Optimized: 34.6K

Your contract is ready. You can find it here:
/build/target/xrc20.wasm

$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo-contract contract generate-metadata 

Your abi is ready. You can find it here:
/build/target/metadata.json

## 项目地址

https://github.com/chainx-org/xrc20