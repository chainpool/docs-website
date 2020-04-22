
### 环境准备
注意： 当前智能合约开关在主网上是关闭的，智能合约将在波卡主网上线后发布，因此需要本地搭建测试网节点进行合约开发。


### 安装测试节点
请前往 [chainx-org/ChainX](https://github.com/chainx-org/ChainX/releases) 下载 v1.1.0 的最新版本 [ChainX v1.1.0](https://github.com/chainx-org/ChainX/releases/tag/v1.1.0).

### 启动节点
./chainx --dev --default-log -d <指定数据目录> --log=runtime=debug --no-telemetry --block-construction-execution=native --other-execution=native

其中 `--dev` 将会指定

* --validator
* --validator-name=Alice
* --key=//Alice

三个参数，意味者将会使用Alice为私钥启动节点。

`--default-log`表示将日志直接打印到控制台中，而不是输入到日志文件中。`-d`将会指定数据目录，若不指定，将会位于默认目录当中（与Substrate默认配置一致）。`--log=runtime=debug`将指定Runtime部分的日志以debug的级别进行打印。`--no-telemetry`表示不向监控台汇报数据。

`--block-construction-executio`和`--other-execution`指定为`native`表示

启动的节点使用默认端口，请检查一下端口是否被占用，否则请指定其他端口：

* p2p：20222 ，使用`--port`指定
* rpc：8086，使用`--rpc-port`指定
* websocket：8087，使用`--ws-port`指定

一般情况下启动单验证者已经足够绝大部分调试需求。注意如果调试的部分和Runtime相关的话，建议将Runtime的log设置为debug级别，即`--log=runtime=debug`

若需要删除数据，则只需要直接删除启动命令中指定的`-d`参数对应的目录即可。


## 使用docker编译智能合约 (推荐）

我们推荐使用docker来创建和build智能合约：

* 查看帮助：

* 创建智能合约flipper：
```bash
docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract new flipper
```
* 编译智能合约

```bash
docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract build
```
一旦该命令执行完毕，编译好的wasm文件在当前目录的target/XXX.wasm下


## 本地安装 

#### 安装rust环境

安装 Rust 环境， 详情请参考 [rust-lang.org/tools/install](https://www.rust-lang.org/tools/install).

#### 安装cargo-contract

* 先决条件
  安装rust-src
   * rust-src: rustup component add rust-src
  安装wasm-opt：
   * wasm-opt: https://github.com/WebAssembly/binaryen#tools
   
* 安装 cargo-contract

chainx-org/cargo-contract fork 自 paritytech/cargo-contract, 是一个 cargo 命令行插件, 可用于创建和编译 ink! 所编写的 WebAssembly 智能合约。

```bash
$ cargo install --git https://github.com/chainx-org/cargo-contract cargo-contract --force
```
#### 基础使用

```bash
cargo-contract 0.3.0
Utilities to develop Wasm smart contracts.

USAGE:
    cargo contract <SUBCOMMAND>

OPTIONS:
    -h, --help       Prints help information
    -V, --version    Prints version information

SUBCOMMANDS:
    new                  Setup and create a new smart contract project
    build                Compiles the smart contract
    generate-metadata    Generate contract metadata artifacts
    test                 Test the smart contract off-chain
    deploy               Upload the smart contract code to the chain
    instantiate          Instantiate a deployed smart contract
    help                 Prints this message or the help of the given subcommand(s)

```

#### 合约编译

cargo contract build 运行需要使用 nightly工具链。如果你安装了 rustup，那么最简单的使用方式是: 
```bash
cargo +nightly contract build
```

为了避免必须添加 +nightly 参数，你可以创建一个 rust-toolchain 的本地文件包含nightly.
在rust-toolchain文件中添加 'nightly'命令。

更多的使用方式请参考 [Read more about how to specify the rustup toolchain](https://github.com/rust-lang/rustup#override-precedence)


