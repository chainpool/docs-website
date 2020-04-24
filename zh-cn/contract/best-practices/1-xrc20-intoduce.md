# ChainX跨链资产Xrc20

## 简介

当前ChainX智能合约中只支持比特币。
由于Substrate Contracts智能合约平台模型与以太坊智能合约模型相似，对于多币种的处理是“本币+代币”模型。
针对这种模型，ChainX的智能合约上的跨链资产采用合约代币模型，因此在ChainX上PCX将会类似以太坊一样对智能合约提供gas及本币流通的价值，对于ChainX上的跨链资产以合约代币的形式引入。
使用代币模型而不是将多币种直接引入合约基于如下考虑：

1. 以太坊代币合约已经比较成熟，因此对于合约开发者在代币模型下可以比较容易的将以太坊合约迁移到ChainX智能合约平台上。
2. 若在合约中引入多币种的概念，将会极大修改Substrate Contracts的模型，容易引入非预期问题。

当前ChainX实现智能合约中的比特币采用XRC20模型，根据合约开发者的反馈，将来也可采用其他标准。目前已经考虑的代币模型有：

1. XRC20(原以太坊ERC20)
2. XRC721(原以太坊ERC721)
3. XRC777(原以太坊ERC777)

## ChainX 资产中的多币种与合约中的代币的转化

### ChainX上的比特币X-BTC

X-BTC对应于智能合约平台中采用的代币模型为XRC20。用户可以自由发起交易让ChainX的X-BTC与合约平台中的XRC20互相转换。

![image-domain](https://user-images.githubusercontent.com/5023721/68760304-7e08ab80-064c-11ea-9c00-28677d41e5d0.png '')

ChainX修改了ERC20合约标准（XRC20），添加了issue，destroy两个接口。并首先将一个合约部署到链上并实例化，同时在链上唯一指定了这个合约实例，因此除指定的实例以外的代币实例均不被ChainX的Runtime承认.

其中：
* issue 只能被Runtime中的交易convert_to_xrc20调用，不可通过直接调用合约方法调用。通过交易convert_to_xrc20调用后，将会把该用户的资产转移到合约实例下，并在合约中对该发送交易的账户自动发放相应的金额。
* destroy 能被用户调用，用于将合约中的资产转移到ChainX资产模块中。其中首先销毁了合约中对应的代币，然后合约中会自动调用convert_to_assets将资产从合约中返回给用户，而convert_to_assets不可通过外部交易调用。

## 合约实现

接下来我们会使用 ink 实现一个 Xrc20项目，并实现Xrc20的所有接口。

### 创建智能合约

运行 cargo contract new xrc20，新建一个合约项目。


#### 模块声明

```rust
use ink_lang as ink;
```

您还会注意到在模块声明之前，有以下内容：

```rust
#![cfg_attr(not(feature = "std"), no_std)]
```
如果我们在代码中使用std功能标志，则该行声明我们正在使用标准库。 否则合同将始终使用no_std进行编译。 Ink合约不使用Rust标准库，因此除非我们明确定义它，否则它将从编译中省略。

### module定义

定义xrc20module, 并导入ink_core,ink_prelude,Encode等模块.Encode用于将事件编码为原始格式。

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

### 事件定义

Event，也可以看作是区块链通知也是智能合约的一个重要方面；它们在发生事情时主动地发出数据，允许DAPP以实时方式对其作出反应。

因此，我们的xrc20定义了四个Event事件，分别是: Transfer、Approval、Issue、Destroy.

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
AccountId类型由ink! Core提供；accountID代表一个帐户（相当于以太坊的地址类型）。另一个可用的类型是balance，它是u64类型，或者64位无符号整数。

### 智能合约变量

智能合约变量可以被认为是通过self在函数内访问的类属性。 以下是我们的Xrc20合同的合约变量：
```rust
struct Xrc20 {
    /// Token总供给
    total_supply: storage::Value<u64>,
    /// Token的精度
    /// 我们选择u16而不是u8，因为parity_codec 3.5缺乏u8支持
    decimals: storage::Value<u16>,
    /// Token的名称
    name: storage::Value<Text>,
    /// Token的符号
    symbol: storage::Value<Text>,
    /// 每个账户的余额
    balances: storage::HashMap<AccountId, u64>,
    // 使用HashMap存储批准实施，
    allowances: storage::HashMap<(AccountId, AccountId), u64>,
}
```
前两个变量的类型为storage :: Value，以及以下三个storage :: HashMap。 事实上，ink_core storage模块必须用于我们希望在链上**持久存在**的任何合同数据。
storage类型是通用的，因此我们在尖括号中明确提供了我们存储的数据类型。
通过定义所需的合同数据，让我们探讨一些合同实现，突出显示一些关键逻辑和语法。

### 智能合约部署

部署函数在任何Ink智能合约中都是强制的，并且在部署到一个链上时实例化一个智能合约时被调用。

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

### 转移实施

转让代币可以说是我们合同中最重要的特征，允许我们将代币转移到账户和从账户转移代币。 `#[ink(message)]`的宏表表该函数是public类型，public transfer() 函数可用于发送传输请求，并具有以下签名：

```rust
 #[ink(message)]
fn transfer(
    &mut self, 
    to: AccountId, value: u64) -> bool {
}

```
此函数调用private transfer_impl（）函数来执行实际的传输逻辑。确保满足以下条件进行转移：

1. 我们立即检查调用者是否是令牌所有者，如果不是，则返回false

2. 更新id_to_token映射，将token_id索引的值覆盖到新所有者的帐户

3. 更新令牌计数，减少发件人的计数并增加接收者的计数

4. Transfer事件通过emit_transfer() 发出

可以说，这是一个比生成过程更简单的函数，这种传输实现允许令牌单独发送。这里的基本机制只是更新id_to_owner映射，跟踪谁拥有什么。从这里开始，发件人和收件人owner_to_token_count记录也会更新，以跟踪个人帐户所拥有的令牌。

```rust

/// 将令牌从指定的AccountId传输到另一个AccountId。
fn transfer_impl(&mut self, from: AccountId, to: AccountId, value: u64) -> bool {
    assert!(from != AccountId::from([0; 32]));
    assert!(to != AccountId::from([0; 32]));

    let balance_from = self.balance_of_or_zero(&from);
    let balance_to = self.balance_of_or_zero(&to);

    let previous_balances = balance_from + balance_to;

    if balance_from < value {
        return false;
    }
    // 从转移账户减去余额
    let new_balance_from = match balance_from.checked_sub(value) {
        Some(b) => b,
        None => return false,
    };
    // 被转移账户添加余额
    let new_balance_to = match balance_to.checked_add(value) {
        Some(b) => b,
        None => return false,
    };

    // 和solod不一样, 我们最后插入最新余额. 再次之前做更多的检查
    if from == to || value == 0 {
        // 插入最新余额之前
        self.env().emit_event(Transfer {
            from: Some(from),
            to: Some(to),
            value,
        });
        return true;
    }

    // 设置余额
    self.balances.insert(from, new_balance_from);
    self.balances.insert(to, new_balance_to);

    // 确保总的余额和转移前想等
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

### 批准实施

批准是一种机制，通过该机制，令牌所有者可以指派其他人代表他们转移令牌。 批准另一个帐户花费您的资金是第三方转帐过程的第一步。令牌所有者可以指定另一个帐户，以及它可以代表所有者花费的任意数量的令牌。所有者不必将所有资金都批准由他人使用；在资金不足的情况下，批准的帐户最多可以从所有者的余额中支出批准的金额。

当一个帐户approve多次呼叫时，批准的值将简单地覆盖过去批准的任何现有值。默认情况下，任何两个帐户之间的批准值是0，用户始终可以致电批准0撤消从另一个帐户访问其资金的权限。

要在我们的智能合约中存储批准，我们需要使用HashMap。由于每个帐户可以有不同的金额供其他任何帐户使用，因此我们需要使用元组作为键，该键仅指向余额值：

```rust

struct xrc20{
   allowances: storage::HashMap<(AccountId, AccountId), u64>
}

```

公共approvals() 函数在被调用时接受两个个参数：

* 我们希望批准的AccountId
* 我们正在添加批准的token_id

Approvals() 的签名如下所示：

```rust
#[ink(message)]
fn approve(&mut self, spender: AccountId, value: u64) -> bool {}

```

调用approve函数时，只需将value指定的插入存储。owner始终是self.env().caller()，确保函数调用始终授权。

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
#### 批准后转移

最后，一旦我们为一个帐户建立了可以在另一个帐户上进行支出的批准，就需要创建一个特殊transfer_from功能，使经批准的用户可以转移这些资金。

我们将利用私有transfer_from_to功能来完成大部分传输逻辑。我们需要介绍的只是授权逻辑。

那么被授权调用此函数意味着什么？

1. self.env().caller()即调用账户必须具有花费`from`账户余额的权限
2. allowance 比如小于要转移的余额

在代码中，可以很容易地表示为：

```rust
let allowance = self.allowance_of_or_zero(&from, &self.env().caller());
if allowance < value {
    return false
}
....
true
```

同样，如果我们的授权没有通过，则提前退出并返回false。

如果一切正常，我们只需将更新后的allowance插入到allowance HashMap中(让new_allowance = allowance - value)，并在指定的from和to帐户之间调用transfer_from_to。

### 合约build(推荐使用Docker)

编译后的wasm在不同的计算机上是不同的，即使它们使用相同的操作系统和相同的源文件。使用Docker提供了构建合同所需的环境，可以保证每个ink合同始终可以获得相同的wasm文件。

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