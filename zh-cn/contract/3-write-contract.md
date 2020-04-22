## 创建 ChainX ink！智能合约工程

在基本用法上， `chainx-org/cargo-contract` 与 `paritytech/cargo-contract` 一致，区别在于 `chainx-org/cargo-contract` 使用了定制的 [`chainx-org/ink` branch ink2.0](https://github.com/chainx-org/ink/tree/ink2.0), 而非 `paritytech/ink`。

创建一个 flipper 的示例项目:

```bash
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract new flipper
```

运行测试：

```bash
$ cd flipper
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo  +nightly test
```

编译 WASM 合约文件：

```bash
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract build
```

生成合约 metadata，也就是合约 ABI:

```bash
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract generate-metadata
```

在 `target/` 目录下面会生成一个 `metadata.json` 文件，其中包含了与合约交互的必要信息。