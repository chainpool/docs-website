# Substrate Contracts, the Smart Contract Platform on ChainX

As the first technical framework in the blockchain domain, Substrate allows developers to focus on the logic of the chain instead of building the underlying infrastructure which is quite time-consuming. In addition, Substrate provides a number of functional modules by default, such as Staking and Consensus, which allows users to freely combine and customize these functions according to their needs. The contract module is one of the functional modules. A separate chain based on Substrate technology or a para-chain in the future can become a smart contract platform by integrating the contract module.

The ChainX smart contract platform is implemented by integrating Substrate's contract module. The main differences between the contract of ChainX and the default contract module of Substrate are as follows:

1. The contract-storing fees are cancelled. Contract-storing fees are charged simply after the contract is deployed onto the chain according to the size and the storage time of the contract. When certain account cannot afford the fees due to insufficient balance, the corresponding contract will be deleted and may not even be recovered. Even if the contract can be recovered, the success rate is extremely low, so it may cause great trouble to the contract development. Therefore, we decide to temporarily withdraw the contract-storing fees and only charge the Gas fee, which is consistent with Ethereum’s current charging mechanism. This storage fees could be redeployed after more improvements are made to the charging model.

2. Contracts are written by adapted chainx-org/ink. Chainx-org/ink fork compared with paritytech/ink, has a main change that is DefaultXrmlTypes replaces DefaultRrmlTypes to adapt ChainX’s runtime
* The association type Balance was changed from u128 to u64.
* Adapting the association type Call to support PCX transfers and converting XRC20 Tokens to ChainX inter-chain assets. The contract developers can design their own Dapps based on PCX and ChainX’s inter-chain assets.

Except the two changes, other contract writing methods are in line with paritytech/ink. Currently contracts are still written in ink1.0 which will be upgraded to ink2.0.

