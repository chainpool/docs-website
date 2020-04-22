
### Environmental preparation
Note: At present, the smart contract switch is closed on the main network, and the smart contract will be released after the Polka main network is launched, so it is necessary to build a test network node locally for contract development.

### Install Test Node
Please go to [chainx-org/ChainX](https://github.com/chainx-org/ChainX/releases) and download version v1.1.0 [ChainX v1.1.0](https://github.com/chainx-org/ChainX/releases/tag/v1.1.0).

### Start Test Node
./chainx --dev --default-log -d <指定数据目录> --log=runtime=debug --no-telemetry --block-construction-execution=native --other-execution=native

Among them `--dev` will specify

* --validator
* --validator-name=Alice
* --key=//Alice

These three parameters mean that Alice will use Alice to start the node for the private key.

`--default-log` Means to print the log directly to the console instead of entering it in the log file.
`-d` The data directory will be specified, if not specified, it will be located in the default directory (same as the default configuration of Substrate). 
`--log=runtime=debug` Print the logs of the specified Runtime section at the debug level.
`--no-telemetry`  means not to report data to the monitoring station.

`--block-construction-executio` and `--other-execution` Appointed as `native` means the started node uses the default port. Please check whether the port is occupied, otherwise specify another port:

* p2p：20222 ，use `--port`
* rpc：8086， use `--rpc-port`
* websocket：8087，use `--ws-port`

Under the normal circumstances, starting a single verifier is sufficient for most debugging needs. Note that if the debugging part is related to Runtime, it is recommended to set the Runtime log to debug level, that is `--log=runtime=debug`

If you want to delete data, you only need to  delete the directory corresponding to the `-d` parameter specified in the startup command directly.

## Use docker to compile smart contracts (recommended)

We recommend using docker to create and build smart contracts:

* View help:

* create smart contract flipper：
```bash
docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract new flipper
```
* build smart contract

```bash
docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract build
```
Once the command is executed, the compiled wasm file is under target / XXX.wasm in the current directory

## Local installation

#### install rust environment

Install Rust environment, please  reference [rust-lang.org/tools/install](https://www.rust-lang.org/tools/install).

#### install cargo-contract

* Prerequisites
  install rust-src
   * rust-src: rustup component add rust-src
  install wasm-opt：
   * wasm-opt: https://github.com/WebAssembly/binaryen#tools
   
* install cargo-contract

chainx-org/cargo-contract fork from paritytech/cargo-contract, which is cargo command line plug-in, Can be used for creation and compilation ink!  WebAssembly smart contract.

```bash
$ cargo install --git https://github.com/chainx-org/cargo-contract cargo-contract --force
```
#### Basic Use

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

#### Compile smart contract

cargo contract build need nightly tool-chain。If you already install rustup，then the easiest way to use is: 

```bash
cargo +nightly contract build
```

To avoid having to add the + nightly parameter, you can create a rust-toolchain local file containing nightly.

Add the 'nightly' command to the rust-toolchain file.

For more usage methods, please refer to [Read more about how to specify the rustup toolchain](https://github.com/rust-lang/rustup#override-precedence)
