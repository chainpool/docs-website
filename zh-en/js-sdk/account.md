## Account Module

You can create an account object via `chainx.account.from(seed | privateKey | mnemonic)`.

```js
const alice = chainx.account.from('0x....')
alice.address()  // base58 encoded address
alice.publicKey() // public key 0x...
alice.privateKey() // private key 0x...
```

In addition, we agree that all hexadecimal strings start with 0x, such as private keys, public keys, etc.

## Create account

```js
const { Account } = require('chainx.js');

const account1 = Account.generate();

const publicKey1 = account1.publicKey(); // public key
console.log('publicKey1: ', publicKey1);

const privateKey1 = account1.privateKey(); // private key
console.log('privateKey1: ', privateKey1);

const address1 = account1.address(); // address
console.log('address1: ', address1);

const mnemonic = Account.newMnemonic(); // mnemonic
console.log('mnemonic: ', mnemonic);

const account2 = Account.from(mnemonic); // mnemonic -> account

const address2 = Account.encodeAddress(account2.publicKey()); // publicKey -> address
console.log('address2: ', address2);

const publicKey2 = Account.decodeAddress(address2);  // address -> publicKey
console.log('publicKey2: ', publicKey2);

Account.setNet('testnet'); // Set the network vertion to testnet
const address3 = Account.encodeAddress(publicKey2); // address for testnet
console.log('address3:', address3);

Account.setNet('mainnet'); // Set the network version to mainnet
const address4 = Account.encodeAddress(publicKey2); // address for mainnet
console.log('address4:', address4);

const account3 = Account.from(privateKey1);   // privateKey -> account
console.log('address:', account3.address()); // address


```