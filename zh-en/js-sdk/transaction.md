## 交易函数

We need several steps to complete a complete transaction. First determine which method we need to call, as shown below. The following APIs all return one [SubmittableExtrinsic](https://github.com/chainx-org/chainx.js/blob/master/packages/chainx/src/SubmittableExtrinsic.js) object.It inherits from [Extrinsic](https://github.com/chainx-org/chainx.js/blob/master/packages/types/src/Extrinsic.js)。Note that all the parts related to the amount are based on the smallest precision.**No decimal**

[SubmittableExtrinsic](https://github.com/chainx-org/chainx.js/blob/master/packages/chainx/src/SubmittableExtrinsic.js) There are several more important methods:

- `encodeMessage(address, { nonce, acceleration = 1, blockHash = genesisHash, era = '0x00' })`: Get the original coded code.  `address` used for signing, nonce refers to the current transaction number of the address, acceleration refers to the acceleration ratio.

- `sign(account, { nonce, acceleration, blockHash })`：Use account to sign the transaction, where nonce refers to the current transaction number of the account. `acceleration` refers to the acceleration rate. The higher the rate, the more the commission will be deducted and the transaction will be faster. blockHash defaults to the initial hash of the current chain.

- `signAndSend(account, options?, callback?)`：Use account to sign and send the transaction. acount can be the account's private key or an account object. options is optional, and the parameters of the sign method are the same, where the nonce is automatically obtained from the chain by default, and the acceleration is 1 by default. callback is the callback function, the first parameter is error, and the second parameter is result. For a normal transaction, callback will be called several times: