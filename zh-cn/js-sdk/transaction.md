## 交易函数

我们需要有几个步骤，来完成一次完整的交易。首先确定我们需要调用的 Method，如下列所示。以下 api 均返回一个 [SubmittableExtrinsic](https://github.com/chainx-org/chainx.js/blob/master/packages/chainx/src/SubmittableExtrinsic.js) 对象，它继承于 [Extrinsic](https://github.com/chainx-org/chainx.js/blob/master/packages/types/src/Extrinsic.js) 。注意所有涉及到金额的部分，均是以最小的精度为单位。**不存在小数。**

[SubmittableExtrinsic](https://github.com/chainx-org/chainx.js/blob/master/packages/chainx/src/SubmittableExtrinsic.js) 存在几个比较重要的方法：

- `encodeMessage(address, { nonce, acceleration = 1, blockHash = genesisHash, era = '0x00' })`: 获取编码后的代签原文。其中 `address`，是用于签名的账户的地址，nonce 指该地址的当前交易数，acceleration 指加速倍率。

- `sign(account, { nonce, acceleration, blockHash })`：使用 account 签名该交易，其中 nonce 指该账户当前交易数。acceleration 指加速倍率，倍率越高手续费被扣的越多，同时交易也越快。blockHash 默认为当前链的初始哈希。

- `signAndSend(account, options?, callback?)`：使用 account 签名并发送该笔交易。acount 可以是账户的私钥，也可以是 account 对象。options 是可选的，和 sign 方法的参数一样，其中 nonce 默认自动从链上获取，acceleration 默认为 1。callback 是回调函数，第一个参数是 error，第二个参数是 result。对于一个正常的交易，callback 将会被调用若干次：



- ```js
  e.signAndSend('0x....', { acceleration: 10 }, (error, result) => {
    /**
    Will output three times
   	{
      "result": null,
      "index": null,
      "events": null,
      // transaction hash
      "txHash": "0x4e93b7cae2868273a8a891684581564cf4431a81e90d2e6c7ee377b26648ac1d",
      "blockHash": null,
      "broadcast": null,
      // transaction state
      "status": "Ready"
  	}
  	{
      "result": null,
      "index": null,
      "events": null,
      "txHash": "0x4e93b7cae2868273a8a891684581564cf4431a81e90d2e6c7ee377b26648ac1d",
      "blockHash": null,
      // Broadcast
      "broadcast": [
        "QmYqN9CKmx3qrNoaqXzDf7PiqvDssc1ALRYBLS65J6Z5wB",
        "QmZGSfxrgWP4Kv9VWmnnYKAs8WNAq1ooQ7kXaAxuS78LZp",
        "QmfNwbvXLbHsxPCmByimLxv1J7ZJpAjSfm82mSEnbobRkY",
        "QmUNmpe6UoSpE3LhkL9knqTogbPDn7Lsg1EQErPDkUJzgL",
        "QmSAwXWpAg45Zsp5X7U6hrAU5dFVacXifowjHbnC2VruRv",
        "QmXxCbLmSMTfFGWep7TNnvUfHSYwGiJ8AyMVPDwyWVAbWn",
        "QmVV2AJ3ju8iwBoE3zzf3tuadjAWyJEaR7338TcXjjcFfL",
        "Qmdfxi8As1jUxx9SrQJYCDgQYPKhiXtgYVvX8vr2RsgYD1",
        "QmPSVr8NRaZdUeNf3eAJfHgmV8WNtePgAWCAX5cU3H4qtn",
        "QmaQSTwbvcxRjs83qAQLW4zfpZ4BnkBHzPhxUE9e85zuUw",
        "QmP39VaaWPBYQ57JoZPYgz8yQg8khMX1LLuN4zwv7xZZEh"
      ],
      // in the Broadcast
      "status": "Broadcast"
    }
  	{
  	// ExtrinsicSuccess means  transaction success。ExtrinsicFail represent  failed.
    "result": "ExtrinsicSuccess",
    "index": 2,
    // A list of linked events
    "events": [{ "phase": 2, "event": [{...}] }, { "phase": 2, "event": [{...}] }, { "phase": 2, "event": [{...}] }],
    "txHash": "0x4e93b7cae2868273a8a891684581564cf4431a81e90d2e6c7ee377b26648ac1d",
    // Block hash on the chain
    "blockHash": "0x56482c31dff1a35db9fbac22be02ae294545a6440dda53f49e240e6df5f2460d",
    "broadcast": null,
    // finish the transaction
    "status": "Finalised"
  }

    **/
    console.log(result);
  })
  ```
