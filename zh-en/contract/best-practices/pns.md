# Secure Decentralized Polkadot Domain Name System

## Introduction
PNS（Polkadot Name Service）is a domain name system built on Polkadot. It’s main function is to translate a long list of meaningless addresses on Polkadot into a very readable and memorable string like "polka-dot".

It allows us to use easy-to-understand "domain names" like "polka.dot" rather than long, hard-to-remember "addresses" in transfers, voting, and some dapp operations.It's like in real life we visit websites using a domain name like google.com rather than the ip address of google computer rooms.

The service that translates "google.com" into Google host ip is DNS (Domain Name Service), while there are currently only 13 IPV4 domain name servers worldwide, 9 in the United States, 2 in Europe and 1 in Asia, and such a centralized distribution has led to a claim on the Internet that the most powerful, direct and deadly way to attack the entire Internet is probably to attack the root domain name server.

Compared to DNS, PNS naturally has the decentralisation of its direct architecture on Polkadot, so the traditional method of attacking the root domain name server naturally does not work.

In addition to the underlying domain name resolution service, PNS also provides secure and reliable domain name registration, auction, transfer and transaction functions.

## Domain registration
![image-domain](../../../_media/contract/domain.png '')

### Domain length

* Short domain name (3-6 characters, auction required, example: chainx.dot)
* Long domain name (7-12 characters, pay the rental fee to choose the lease term and register, example: chainxpool.dot)

### Sign up steps

> 1. Fill in the domain name you want to register (e.g. chainx)
  2. Select the domain name time limit (default 1 year validity, renewal, rental fee and rental market-related, greater than 3 years can be given a certain discount)
  3. Pay to submit a transaction and get the domain name if the transaction is successful
  4. Optional: default binding transaction address, you can change the binding address

### Auction

* UK Style auction, starting price at one-year rental fee, no reserve price
* The auction system regularly releases a portion of the short domain name for auction, and within the specified period, the highest bidder will receive the domain name.

> If no one bids, the domain name will be placed at the starting price in the agent trading system, any user who wants to obtain the domain name can obtain the domain name through proxy trading.

### Agent trading

Users can register domain names, and there is a natural need to sell domain names. The process of selling a domain name is as follows:

Alice simply needs to submit to the "agent trading contract" a transaction that includes the transaction price, commission rate (the commission rate determines your display priority in the PNS trading system) and the timely transaction, which automatically enters the "proxy trading system" after the transaction is successful, and after the expiration of the time limit, the domain name leaves the "proxy trading system" and returns to Alice's ownership. Any user who wishes to purchase the domain name during the limitation period only needs to purchase and pay the price indicated in the "Agent Trading Contract" to obtain the domain name.


###  Bid Transfer

You're frustrated when you find out that the domain name you want to register has been registered by someone else. In the real world, in order to get your favorite domain name, you can contact the broker through the domain management website, and then the broker to help you ask the domain name owner if you want to resell, if the other party is willing to resell, after experiencing the domain name management and domain name registrar transfer operation, you can get the domain name. But in the blockchain world, no one seems to be able to be an economic person, and even if you want to pay a high premium if your account is anonymous, you may not be able to connect at all.

What if PNS also offers a bid transfer system, then how do you do that? Let's imagine the following scenario:

Alice wants to register a domain name called "polka.dot", but finds that the domain name has been registered by an unknown user and that the domain name is neither in the Auction System or in the "Agent Trading System", so Alice can send a bid transfer application to the Bid Transfer Contract and carry her own offer and a certain percentage of the margin.

When an unknown user logs on to PNS or any PNS-enabled app (we provide a bid notification or toolkit for all PNS-enabled apps), he receives a bid transfer notice, and if the unknown user agrees to Alice's bid transfer request, he or she can transfer his domain name to the Proxy Trading System via the method provided by PNS, which Alice only needs to make up for the end of the "Agent Trading System" to obtain the domain name .polka.dot.
If the domain name owner does not agree to Alice's request, no action is required, and the request is automatically invalidated after two weeks and Alice's margin is returned.

If Alice defaults and does not make up the closing amount within two weeks, then "polka.dot" will be released in the "agent trading system" and the margin previously paid will be allocated to the "agent trading contract" and the domain name holder.

> Keywords such as decentralization, anonymity, and security are emphasized in almost all blockchain applications, but anonymity may not be a commendable point for blockchain applications that really need interaction. For example, in the transfer of domain names, it is not possible to make the first bid to be satisfied with both parties, then each other's bargaining is necessary. Bargaining technology is really possible in smart contracts, but it's actually a waste of resources and time-consuming for blockchain. So if we can use the user's contact information (email) as a property of the domain name (and if the user can actually facilitate it, then the user may not mind filling out their own email address), then the unrelated two users can complete the domain name price determination in a more efficient way, and then securely complete the domain name transaction through the "proxy trading contract" provided by PNS. This interaction, which takes into account both user experience and ensures transaction security, may be more in line with the real needs of most users.

### Domain name management

After successful registration or purchase of a domain name, some basic information needs to be set up to make better use of it

1. Change the mapping address
2. Add subdomains
3. Change owner
4. Renew

## Smart Contract Implementation

Next we'll use ink to implement a simple PNS, where we mainly implement domain name registration, setting addresses, domain transfer, and domain name query.


### Create Smart Contract

Run `cargo contract new simple-pns` to create a new ink! project.


### Import Module

```rust
    use ink_core::{
        env::DefaultXrmlTypes,
        storage,
    };
    use scale::{
        Encode
    };
    use ink_prelude::vec::Vec;

    pub type Text = Vec<u8>;

```

### Defining the contract structure

```rust

    #[ink(storage)]
    struct SimplePns {
        /// A hashmap to store all name to addresses mapping
        name_to_address: storage::HashMap<Hash, AccountId>,
        /// A hashmap to store all name to owners mapping
        name_to_owner: storage::HashMap<Hash, AccountId>,
        name_to_abi: storage::HashMap<Hash, Text>,
        code_hash_to_abi: storage::HashMap<Hash, Text>,
        account_to_code_hash_list: storage::HashMap<AccountId, Vec<Hash>>,
        default_address: storage::Value<AccountId>,
    }
   
```

Where name_to_address is a hashmap that stores the domain name to the mapped address, name_to_owner is a hashmap that stores the domain name to the domain name owner, and default_address is an empty address of type AccountId.

### Defining Event

```rust

/// Emitted whenever a new name is being registered.
    #[ink(event)]
    struct Register {
        #[ink(topic)]
        name: Hash,
        #[ink(topic)]
        from: AccountId,
    }

    /// Emitted whenever a new name is being registered.
    #[ink(event)]
    struct RegisterAbi {
        #[ink(topic)]
        name: Hash,
        #[ink(topic)]
        from: AccountId,
        #[ink(topic)]
        code_hash: Hash,
        abi: Text
    }

    #[ink(event)]
    struct SetAddress {
        name: Hash,
        from: AccountId,
        old_address: Option<AccountId>,
        new_address: AccountId,
    }

    #[ink(event)]
    struct Transfer {
        name: Hash,
        #[ink(topic)]
        from: AccountId,
        #[ink(topic)]
        old_owner: Option<AccountId>,
        #[ink(topic)]
        new_owner: AccountId,
    } 


```

### Initialize the contract

```rust

 #[ink(constructor)]
 fn new(&mut self) {
    self.default_address.set(AccountId::from([0x0; 32]));
 }

```

### How to implement domain name operations


```rust
 impl SimplePns {
        /// Creates a new domain name service contract.
        #[ink(constructor)]
        fn new(&mut self) {
            self.default_address.set(AccountId::from([0x0; 32]));
        }

        /// Register specific name with caller as owner
        #[ink(message)]
        fn register(&mut self, name: Hash, address: AccountId) -> bool {
            let caller = self.env().caller();
            if self.is_name_exist_impl(name) {
                return false
            }
            // env.println(&format!("register name: {:?}, owner: {:?}", name, caller));
            self.name_to_owner.insert(name, caller);
            self.name_to_address.insert(name, address);
            self.env().emit_event(Register {
                name: name,
                from: caller,
            });
            true
        }

        #[ink(message)]
        fn register_abi(&mut self, name: Hash, code_hash: Hash, abi: Text) -> bool {
            let caller = self.env().caller();
            if self.is_name_exist_impl(name) {
                return false
            }
            // env.println(&format!("register_abi name: {:?}, owner: {:?}", name, caller));
            self.name_to_abi.insert(name, abi.clone());
            self.code_hash_to_abi.insert(code_hash, abi.clone());
            match self.account_to_code_hash_list.get_mut(&caller) {
                None => {
                    let mut new_vec = Vec::new();
                    new_vec.push(code_hash);
                    self.account_to_code_hash_list.insert(env.caller(), new_vec);
                },
                Some(a) => {
                    a.push(code_hash);
                }
            }
            self.env().emit_event(RegisterAbi {
                from: caller,
                name: name,
                code_hash: code_hash,
                abi: abi
            });
            true
        }

         /// Query abi by name
        #[ink(message)]
        fn get_abi_by_name(&self, name: Hash) -> Text {
            self.name_to_abi.get(&name).unwrap_or(&Vec::new()).to_vec()
        }

        /// Query abi by code_hash
        #[ink(message)]
        fn get_abi_by_code_hash(&self, code_hash: Hash) -> Text {
            self.code_hash_to_abi.get(&code_hash).unwrap_or(&Vec::new()).to_vec()
        }

        /// Query code_hash list by account
        #[ink(message)]
        fn get_code_hash_list_by_account(&self, account: AccountId) -> Vec<Hash> {
            self.account_to_code_hash_list.get(&account).unwrap_or(&Vec::new()).to_vec()
        }

        /// Set address for specific name
        #[ink(message)]
        fn set_address(&mut self, name: Hash, address: AccountId) -> bool {
            let caller: AccountId = self.env().caller();
            let owner: AccountId = self.get_owner_or_none(name);
            // env.println(&format!("set_address caller: {:?}, owner: {:?}", caller, owner));
            if caller != owner {
                return false
            }
            let old_address = self.name_to_address.insert(name, address);
            self.env().emit_event(SetAddress {
                name: name,
                from: caller,
                old_address: old_address,
                new_address: address,
            });
            return true
        }

        /// Transfer owner to another address
        #[ink(message)]
        fn transfer(&mut self, name: Hash, to: AccountId) -> bool {
            let caller: AccountId = self.env().caller();
            let owner: AccountId = self.get_owner_or_none(name);
            // env.println(&format!("transfer caller: {:?}, owner: {:?}", caller, owner));
            if caller != owner {
                return false
            }
            let old_owner = self.name_to_owner.insert(name, to);
            self.env().emit_event(Transfer {
                name: name,
                from: caller,
                old_owner: old_owner,
                new_owner: to,
            });
            return true
        }

        /// Get address for the specific name 
        #[ink(message)]
        fn get_address(&self, name: Hash) -> AccountId {
            let address: AccountId = self.get_address_or_none(name);
            // env.println(&format!("get_address name is {:?}, address is {:?}", name, address));
            address
        }

        /// Check whether name is exist
        #[ink(message)]
        fn is_name_exist(&self, name: Hash) -> bool {
            self.is_name_exist_impl(name)
        }

         fn get_address_or_none(&self, name: Hash) -> AccountId {
            let address = self.name_to_address.get(&name).unwrap_or(&self.default_address);
            *address
        }

        /// Returns an AccountId or default 0x00*32 if it is not set.
        fn get_owner_or_none(&self, name: Hash) -> AccountId {
            let owner = self.name_to_owner.get(&name).unwrap_or(&self.default_address);
            *owner
        }

        /// check whether name is exist
        fn is_name_exist_impl(&self, name: Hash) -> bool {
            let address = self.name_to_owner.get(&name);
            if let None = address {
                return false;
            }
            true
        }
    }

```

### Compilation contracts and ABI


Compile the WASM contract file:

```bash
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract build
```

Generate contract metadata whic is contract ABI:


```bash
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract generate-metadata
```
After a successful run, the corresponding wasm and json files appear in the target directory.


## 项目地址
https://github.com/chainpool/simple_pns