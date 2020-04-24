# Data Type

## Value Stored

Any data that is persistently stored on the Substrate chain is called external data, and Ink provides us with a method of storing external data on the chain through a storage module, which is located in the core module of the language. In other words, all contract variables you plan to keep on the chain will use the types in storage. Conversely, memory modules can also be used for data structures that operate on memory.
The first thing we want to do with smart contracts is to introduce some stored value. Here is how you can store some simple values ​​in memory:

```rust
#[ink(storage)]
struct MyContract {
    // store a bool value
    my_bool: storage::Value<bool>,
    // store number
    my_number: storage::Value<u32>,
}

```
### 支持的数据类型

Contract storage like storage::Value<T> is allowed to be generic over types that are encodable and decodable with Parity Codec which includes the most common types such as bool, u{8,16,32,64,128}, i{8,16,32,64,128}, String, tuples, and arrays.

ink! provides smart contracts Substrate specific types like AccountId, Balance, and Hash as if they were primitive types. Also ink! provides storage types for more elaborate storage interactions through the storage module:

```rust
use ink_core::storage::{Value, Vec, HashMap, BTreeMap, Heap, Stash, Bitvec};

```

Here is an example of how you would store an AccountId and Balance:

```rust
// We are importing the default PALETTE types
use ink_lang as ink;

#[ink::contract(version = "0.1.0")]
impl MyContract {

    // Our struct will use those default PALETTE types
    #[ink(storage)]
    struct MyContract {
        // Store some AccountId
        my_account: storage::Value<AccountId>,
        // Store some Balance
        my_balance: storage::Value<Balance>,
    }
    ...
}

```

### Initializing Storage

Before you can interact with any storage items in an ink! contract, you must make sure they are initialized! If you do not initialize a storage item and try to access it, your contract call will not succeed and any state changes caused by the transaction will be reverted. (Gas fees will still be charged though!)

For storage values like the ones above, we can set an initial value with:

```rust
self.my_bool.set(false);
self.my_number.set(42);
self.my_account.set(AccountId::from([0x1; 32]));
self.my_balance.set(1337);

```

### Contract Deployment
Every ink! smart contract must have a constructor which is run once when a contract is created. ink! smart contracts can have multiple different constructors:

```rust
use ink_lang as ink;

#[ink::contract(version = "0.1.0")]
impl MyContract {
    #[ink(storage)]
    struct MyContract {
        number: storage::Value<u32>,
    }

    impl MyContract {
        #[ink(constructor)]
        fn new(&mut self, init_value: u32) {
            self.number.set(init_value);
        }

        #[ink(constructor)]
        fn default(&mut self) {
            self.new(0)
        }
       ....
    }
}

```

