# create ChainX ink！smart contract project

In basic usage, `chainx-org/cargo-contract` and   `paritytech/cargo-contract` are same.
Create a flipper example project:

```bash
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract new flipper
```

Run the test:

```bash
$ cd flipper
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo  +nightly test
```

Compile WASM contract file:

```bash
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo +nightly contract build
```

Generate contract metadata, which is the contract ABI:

```bash
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo  +nightly contract generate-metadata
```

A `metadata.json` file will be generated under the` target / `directory, which contains the necessary information to interact with the contract.