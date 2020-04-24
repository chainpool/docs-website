# 数据类型

## 值存储
在Substrate链上持久存储的任何数据称为外在数据，而Ink为我们提供了通过存储模块在链上存储外部数据的方法，存储模块位于语言的核心模块中。 换句话说，您计划在链上保留的所有合约变量都将使用存储中的类型。 相反，存储器模块也可用于在存储器上操作的数据结构。
我们要对智能合约做的第一件事是引入一些存储值。以下是你如何将一些简单的值存储在存储器:

```rust
#[ink(storage)]
struct MyContract {
    // 存储一个布尔值
    my_bool: storage::Value<bool>,
    // 存储一些数字
    my_number: storage::Value<u32>,
}

```
### 支持的数据类型

ink!提供智能合约的底层特定类型，如AccountId、Balance和Hash，就好像它们是原始类型一样。ink!通过存储模块为更复杂的存储交互提供存储类型:

```rust
use ink_core::storage::{Value, Vec, HashMap, BTreeMap, Heap, Stash, Bitvec};

```

下面是一个如何存储AccountId和余额的例子:

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

### 变量初始化

**比如确保变量初始化之后，才可以与ink!合约进行交互**如果没有初始化之前尝试访问，合约调用不会成功，但仍然会时候去gas.

我们可以这样进行初始化:

```rust
self.my_bool.set(false);
self.my_number.set(42);
self.my_account.set(AccountId::from([0x1; 32]));
self.my_balance.set(1337);

```

### 构造函数
每个ink!智能合约必须有一个构造函数用户合约实例化，这个构造函数在合约创建的时候回运行一次。ink!智能合约可以有多个不同的构造函数：

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
    /* --snip-- */
    }
}

```

