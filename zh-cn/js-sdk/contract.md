# 智能合约

## 交易

### chainx.api.tx.xContracts.putCode(gasLimit, code)
将给定的二进制 Wasm 代码存储到链上，同时可以获取它的 codeHash。只能通过已存储的代码实例化合约。

```js
const { compactAddLength } = require('@chainx/util');
const code = compactAddLength(fs.readFileSync(path.resolve(__dirname, './erc20.wasm')));

const extrinsic = chainx.api.tx.xContracts.putCode(100000000, code);

let codeHash;

ex.signAndSend(Test, (error, result) => {
  console.log(error, result);
  if (result && result.result === 'ExtrinsicSuccess') {
    // 获取 codeHash
    codeHash = result.events.find(e => e.method === 'CodeStored').event.data;
  }
});

```

### chainx.api.tx.xContracts.instantiate(endowment, gasLimit, codeHash， data)

chainx.api.tx.xContracts.instantiate(endowment, gasLimit, codeHash， data)

```js
// 合约 abi
const erc20 = require('./erc20');
// 解析 abi
const Abi = require('@chainx/api-contract');

const abi = new Abi(erc20);
// 5GE7vwvDmKCCPrVLc9XZJAiAspM9LhQWbQjPvZ3QxzBUbhT7
const extrinsic = chainx.api.tx.xContracts.instantiate(
  1000,
  100000000,
  '0x5e71dc66c1527bf4047942c5ada9c5c59941bff8eb8b2d1a6d849306bfd52e93',
  abi.constructors[0](...), // 合约的构造函数
);

let contractAddress

ex.signAndSend(Test, (error, result) => {
  if (result && result.result === 'ExtrinsicSuccess') {
    // 获取 contractAddress
    contractAddress = result.events.find(e => e.method === 'Instantiated').event.data[1];
  }
});
```

### chainx.api.tx.xContracts.call(dest, value, gasLimit, data)

调用合约，同时也可以传输一些余额。

```js
const abi = new Abi(erc20);

const ex = chainx.api.tx.xContracts.call(
  '5GE7vwvDmKCCPrVLc9XZJAiAspM9LhQWbQjPvZ3QxzBUbhT7', // contract address
  0, // value
  10000000, // gas
  // 调用的函数
  abi.messages.transfer('5FvHGYk44FHZXznrhoskVyr2zGPYn5CpUXphRKM8eGRJZMtX', 10)
);

ex.signAndSend(Alice, (error, result) => {
  console.log(error, result);
});

```

### chainx.api.tx.xContracts.convertToXrc20(token, value, gasLimit)

```js
const ex = chainx.api.tx.xContracts.convertToXrc20(
  'BTC', // token
  1000, // value
  500000 // gas
);

ex.signAndSend(Alice, (error, result) => {
  console.log(error, result);
});
```


### chainx.chain.convertToAsset(token, balance, value, gas)

将 xrc 资产转化为余额

```js
chainx.chain.convertToAsset('BTC', 100000, 0, 500000).then(extrinsic => {
  extrinsic.signAndSend(Alice, (error, result) => {
    console.log(error, result);
  });
});

```


