# ChainX ink!

[chainx-org/ink](https://github.com/chainx-org/ink) fork from [paritytech/ink](https://github.com/paritytech/ink). Although since ink2.0, the ink framework has been able to add and use custom RuntimeType types easily.It is not necessary to modify the ink framework for chains based on recent substrate instead  directly define the RuntimeType. But [chainx-org/ChainX](https://github.com/chianx-org/ChainX) started very early, the underlying coding library still use the earlier codec3.5 instead of the latest parity scale-codec, so fork [paritytech/ink](https://github.com/paritytech/ink) add `old-codec` to support ChainX.

Now [chainx-org/ink](https://github.com/chainx-org/ink) already support PCX  and bitcoin transfer in the contract，Meanwhile you can easy to get users account information. The developers can design、develop and verify their own Dapp product. Please see the Example: [chainx-org/ink/examples/pcx_transfer](https://github.com/chainx-org/ink/tree/master/examples/pcx_transfer) and [chainx-org/ink/examples/xbtc_demo](https://github.com/chainx-org/ink/tree/master/examples/xbtc_demo).

For developers of the ChainX contract platform, it is important to note that you must use  `DefaultXrmlTypes`  to develop  ChainX Smart Contract，The difference between `DefaultXrmlTypes` and the default `DefaultRrmlTypes` is as follows:

- `DefaultXrmlTypes` change `Balance` from `u128` to `u64`.
- Adapt the association type `Call` to support PCX transfer and XRC20 Token to transfer cross-chain assets back to ChainX cross-chain assets, Contract developers can design their own dapps based on cross-chain assets in PCX and ChainX.

```
   #[ink::contract(version = "0.1.0", env = DefaultXrmlTypes)]
   ...
```

In addition to the above two changes in the contract, writing contract in chainX is same as using [paritytech/ink](https://github.com/paritytech/ink).

# cargo-contract

[chainx-org/cargo-contract](https://github.com/chainx-org/cargo-contract/) fork from [paritytech/cargo-contract](https://github.com/paritytech/cargo-contract) which is  `cargo` command line plug-in, Can be used to create and compile  `WebAssembly` smart contract wrote by `ink!`

```bash
cargo install --git https://github.com/chainx-org/cargo-contract cargo-contract --force
```

Details: [cargo-contract/README](https://github.com/chainx-org/cargo-contract/blob/master/README.md) 