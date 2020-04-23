
# contract

## chainx.api.tx.xContracts.putCode(gasLimit, code)
Store the given binary Wasm code on the chain, and get its codeHash at the same time. The contract can only be instantiated through the stored code.

```js
const { compactAddLength } = require('@chainx/util');
const code = compactAddLength(fs.readFileSync(path.resolve(__dirname, './erc20.wasm')));

const extrinsic = chainx.api.tx.xContracts.putCode(100000000, code);

let codeHash;

ex.signAndSend(Test, (error, result) => {
  console.log(error, result);
  if (result && result.result === 'ExtrinsicSuccess') {
    // get codeHash
    codeHash = result.events.find(e => e.method === 'CodeStored').event.data;
  }
});

```

## chainx.api.tx.xContracts.instantiate(endowment, gasLimit, codeHash， data)

chainx.api.tx.xContracts.instantiate(endowment, gasLimit, codeHash， data)

```js
// contract abi
const erc20 = require('./erc20');
// Parsing abi
const Abi = require('@chainx/api-contract');

const abi = new Abi(erc20);
// 5GE7vwvDmKCCPrVLc9XZJAiAspM9LhQWbQjPvZ3QxzBUbhT7
const extrinsic = chainx.api.tx.xContracts.instantiate(
  1000,
  100000000,
  '0x5e71dc66c1527bf4047942c5ada9c5c59941bff8eb8b2d1a6d849306bfd52e93',
  abi.constructors[0](...), // contract constructor function
);

let contractAddress

ex.signAndSend(Test, (error, result) => {
  if (result && result.result === 'ExtrinsicSuccess') {
    // get contractAddress
    contractAddress = result.events.find(e => e.method === 'Instantiated').event.data[1];
  }
});
```

## chainx.api.tx.xContracts.call(dest, value, gasLimit, data)

Call the contract and also transfer some balance.

```js
const abi = new Abi(erc20);

const ex = chainx.api.tx.xContracts.call(
  '5GE7vwvDmKCCPrVLc9XZJAiAspM9LhQWbQjPvZ3QxzBUbhT7', // contract address
  0, // value
  10000000, // gas
  abi.messages.transfer('5FvHGYk44FHZXznrhoskVyr2zGPYn5CpUXphRKM8eGRJZMtX', 10)
);

ex.signAndSend(Alice, (error, result) => {
  console.log(error, result);
});

```

## chainx.api.tx.xContracts.convertToXrc20(token, value, gasLimit)

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


## chainx.chain.convertToAsset(token, balance, value, gas)

Convert xrc assets to balance

```js
chainx.chain.convertToAsset('BTC', 100000, 0, 500000).then(extrinsic => {
  extrinsic.signAndSend(Alice, (error, result) => {
    console.log(error, result);
  });
});

```


