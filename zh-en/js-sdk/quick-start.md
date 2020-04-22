## Chainx.js

 Only support websocket for now, use must wait for finishing the initialization of async ：

```js
const chainx = new Chainx('wss://w1.chainx.org.cn/ws');

chainx.isRpcReady().then(() => {
  // ...
});

// Listen to the events
chainx.on('disconnected', () => {}) // websocket disconnected
chainx.on('error', () => {}) // an error occurs
chainx.on('connected', () => {}) // websocket connected
chainx.on('ready', () => {}) // initialization is done

// Close the websocket connection.
chainx.provider.websocket.close()
```

During the initialization, chainx.js will fetch the network version automatically. The address format is different between the mainnet and testnet. Notes: the initialization is async, the network version is testnet by default before finishing the initialization. You can also specify the network version manually via `chainx.account.setNet('mainnet')` or `chainx.account.setNet('testnet')`.

## Example

```javascript
const Chainx = require('chainx.js').default;

(async () => {
  // Only support websocket for now
  const chainx = new Chainx('wss://w1.chainx.org/ws');

  // Wait for finishing the initialization of async
  await chainx.isRpcReady();

  // Get the balance info of some account
  const bobAssets = await chainx.asset.getAssetsByAccount('5DtoAAhWgWSthkcj7JfDcF2fGKEWg91QmgMx37D6tFBAc6Qg', 0, 10);

  console.log('bobAssets: ', JSON.stringify(bobAssets));

  // Build the extrinsic arguments (sync)

  const extrinsic = chainx.asset.transfer('5DtoAAhWgWSthkcj7JfDcF2fGKEWg91QmgMx37D6tFBAc6Qg', 'PCX', '1000', '转账 PCX');

   // Check out the hash of method
  console.log('Function: ', extrinsic.method.toHex());

  // Sign and send the transaction.
  //0x0000000000000000000000000000000000000000000000000000000000000000 是用于签名的私钥
  extrinsic.signAndSend('0x0000000000000000000000000000000000000000000000000000000000000000', (error, response) => {
    if (error) {
      console.log(error);
    } else if (response.status === 'Finalized') {
      if (response.result === 'ExtrinsicSuccess') {
        console.log('交易成功');
      }
    }
  });
})();
```

## 离线签名 websocket 版本

```javascript
const Chainx = require('chainx.js').default;
const nacl = require('tweetnacl');

// Sign the message using ed25519 algorithm.
async function ed25519Sign(message) {
  // 32 byte private key of the signer
  const privateKey = Buffer.from('5858582020202020202020202020202020202020202020202020202020202020', 'hex');
  // Sign the message using ed25519 algorithm.
  return nacl.sign.detached(message, nacl.sign.keyPair.fromSeed(privateKey).secretKey);
}

(async () => {
  // Only support websocket for now
  const chainx = new Chainx('wss://w1.chainx.org/ws');

  // Wait for finishing the initialization of async
  await chainx.isRpcReady();

  // Public key of the signer
  const address = '5CjVPmj6Bm3TVUwfY5ph3PE2daxPHp1G3YZQnrXc8GDjstwT';

   // Claim the staking dividend
  const extrinsic = chainx.stake.voteClaim(address);

  // Get nonce of the transactor
  const nonce = await extrinsic.getNonce(address);

  // Get the message to be signed.
  // Args: extrinsic.encodeMessage(address, { nonce, acceleration = 1, blockHash = genesisHash, era = '0x00' })
  const encoded = extrinsic.encodeMessage(address, { nonce, acceleration: 10 });

  // Sign offline
  const signature = await ed25519Sign(encoded);

  // Inject signature
  extrinsic.appendSignature(signature);

  // Check out the data sent to the chain.
  // console.log(extrinsic.toHex())

  // Send the transaction
  extrinsic.send((error, result) => {
    console.log(error, result);
  });
})();
```

## 离线签名 http 版本

```javascript
const { ApiBase, HttpProvider } = require('chainx.js');
const nacl = require('tweetnacl');

// Sign the message using ed25519 algorithm.
async function ed25519Sign(message) {
  // 32 byte private key of the signer
  const privateKey = Buffer.from('5858582020202020202020202020202020202020202020202020202020202020', 'hex');
  // Sign the message using ed25519 algorithm.
  return nacl.sign.detached(message, nacl.sign.keyPair.fromSeed(privateKey).secretKey);
}

(async () => {
  const chainx = new ApiBase(new HttpProvider('https://w1.chainx.org/rpc'));

  await chainx._isReady;

  // Public key of the signer
  const address = '5CjVPmj6Bm3TVUwfY5ph3PE2daxPHp1G3YZQnrXc8GDjstwT';

  // Claim the staking dividend
  const extrinsic = chainx.tx.xStaking.claim(address);

  // Get nonce of the transactor
  const nonce = await chainx.query.system.accountNonce(address);

  // Get the message to be signed.
  // Args: extrinsic.encodeMessage(address, { nonce, acceleration = 1, blockHash = genesisHash, era = '0x00' })
  const encoded = extrinsic.encodeMessage(address, { nonce, acceleration: 10 });

  // Get the transaction fee.
  const fee = await extrinsic.getFee(address, { acceleration: 1 });

  console.log('fee', fee)

  // Sign the message offline
  const signature = await ed25519Sign(encoded);

  // Inject the signed signature
  extrinsic.appendSignature(signature);

   // Send the transaction
  const txHash = await extrinsic.send();

  console.log(txHash);
})();

```
