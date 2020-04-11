## Account 模块

通过`chainx.account.from(seed | privateKey | mnemonic)` 可以生成一个 account 对象。

```js
const alice = chainx.account.from('0x....')
alice.address() // bs58 地址
alice.publicKey() // 公钥 0x...
alice.privateKey() // 私钥 0x...
```

此外，我们约定，凡是十六进制的字符串，均以 0x 开头，如私钥，公钥等。

## 账户操作

```js
const { Account } = require('chainx.js');

const account1 = Account.generate();

const publicKey1 = account1.publicKey(); // 公钥
console.log('publicKey1: ', publicKey1);

const privateKey1 = account1.privateKey(); // 私钥
console.log('privateKey1: ', privateKey1);

const address1 = account1.address(); // 地址
console.log('address1: ', address1);

const mnemonic = Account.newMnemonic(); // 随机助记词
console.log('mnemonic: ', mnemonic);

const account2 = Account.from(mnemonic); // 从助记词生成账户

const address2 = Account.encodeAddress(account2.publicKey()); // 从公钥生成地址
console.log('address2: ', address2);

const publicKey2 = Account.decodeAddress(address2); // 从地址获取生成公钥
console.log('publicKey2: ', publicKey2);

Account.setNet('testnet'); // 设置为测试网
const address3 = Account.encodeAddress(publicKey2); // 测试网地址
console.log('address3:', address3);

Account.setNet('mainnet'); // 设置为主网
const address4 = Account.encodeAddress(publicKey2); // 主网地址
console.log('address4:', address4);

const account3 = Account.from(privateKey1); // 从私钥生成账户
console.log('address:', account3.address()); // 地址
```
