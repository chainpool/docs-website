# Contract Template
让我们来看看当你使用ink!开发一个智能合同时，都需要了解哪些？

## ink！

ink！有意成为用于编写Rust智能合约的新领域专用语言，把Rust编译成Wasm代码。它仍处于实验阶段，缺少了很多计划中的功能，但现在是可以开始使用它来编写智能合约。

ink!为基于Wasm虚拟机并与Substrate链兼容的新智能合约堆栈制定了基础。

Substrate包括合约模块，其中包括智能合约链所需的核心逻辑。除此之外，ink!将是用Rust编写智能合约的语言，它利用已有Rust的工具和支持，并将它编译到Wasm。


## 创建一个智能合约模板

* 使用Docker创建

```bash
docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract new flipper
```

* 使用本地安装的cargo创建

```bash
cargo contract new flipper

```

命令执行完成后，将会得到如下目录

```bash
flipper
  .ink
  lib.rs
  Cargo.toml
```
其中Cargo.toml是项目相关依赖配置，合约的逻辑在lib.rs文件中:


## 智能合约架构

构造一个ink智能合约类似于solidity智能合约，其中我们期望的solidity的主要组成部分在ink中也是一致的：合同变量、事件、公共函数和私有函数，以及获取调用方地址的环境变量等等。

下面是Erc20合约是如何构造的抽象：

```rust
// 导入ink模块
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
