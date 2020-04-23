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

```rust
#![cfg_attr(not(feature = "std"), no_std)]

use ink_lang as ink;

#[ink::contract(version = "0.1.0", env = DefaultXrmlTypes)]
mod flipper {
    use ink_core::storage;
    #[ink(storage)]
    struct Flipper {
        value: storage::Value<bool>,
    }

    impl Flipper {
        #[ink(constructor)]
        fn new(&mut self, init_value: bool) {
            self.value.set(init_value);
        }
        #[ink(constructor)]
        fn default(&mut self) {
            self.new(false)
        }

        #[ink(message)]
        fn flip(&mut self) {
            *self.value = !self.get();
        }

        #[ink(message)]
        fn get(&self) -> bool {
            *self.value
        }
    }
    #[cfg(test)]
    mod tests {
        use super::*;

        #[test]
        fn default_works() {
            let flipper = Flipper::default();
            assert_eq!(flipper.get(), false);
        }
        #[test]
        fn it_works() {
            let mut flipper = Flipper::new(false);
            assert_eq!(flipper.get(), false);
            flipper.flip();
            assert_eq!(flipper.get(), true);
        }
    }
}

```

## 模块声明

Ink不依赖于Rust标准库 - 而是导入Ink模块来编写合同逻辑。 
```rust
use ink_lang as ink;
use ink_core::storage;

```
这里公开智能合约中需要使用哪些模块，导入storage模块。

您还会注意到在模块声明之前，有以下内容：

```rust
#![cfg_attr(not(feature = "std"), no_std)]
```
如果我们在代码中使用std功能标志，则该行声明我们正在使用标准库。 否则合同将始终使用no_std进行编译。 Ink合约不使用Rust标准库，因此除非我们明确定义它，否则它将从编译中省略。



