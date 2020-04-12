### Account System

ChainX follows the bip39/44 protocol, the chain number is 239, and the account private key can be generated using a uniform mnemonic.

### System Coin

- the currency issued by the ChainX system is Polkadot ChainX with the symbol PCX
- The maximum limit of -pcx is 21 million, and the accuracy is 8 decimal places
- The smallest unit of -pcx is polka, which is 1 PCX is equal to 100, 000, 000 polka

### Transaction Fee

In order to prevent DDOS, users need to pay PCX which is the base currency of the system to initiate a transaction. The service fee standard is:

```bash
(benchmark commission * transaction complexity + transaction bytes * byte processing rate) * user acceleration multiple
```
* benchmark fee: refers to the lowest complexity transfer operation in the system, currently set at 10000 polka
* transaction complexity: refers to the average computational complexity or importance complexity of the transaction relative to the transfer operation
* transaction bytes: refers to the full transaction size after the transaction is signed and represents the burden on network broadcast and block chain storage
* byte handling fee: 100 polka per byte
* user acceleration multiple: it is set manually by the user to be an integer multiple starting from 1, which can be set by the user according to the network congestion.

Before the transaction is executed, enough space should be reserved for the transaction fee for such operations as transfer and voting. PCX transfer or voting cannot be carried out in full.

### Transaction Complexity

| module | operation | trading name b| multiple |
| ----       | ----         | ----            | ---- |
| assets | transfer | xasset/transfer | 1 |
| asset | withdraw | xasset/withdraw | 3 |
| BTC transfer bridge | submitted | | 10 |
| BTC transfer bridge | submit transaction | | 8 |
| BTC transfer bridge | structure multiple withdrawal | | 5 |
| BTC transfer bridge | response to multiple withdrawal | | 5 |
| SDOT transfer bridge | submit transaction | | 2 |
| elects | registered node | | 1000 |
| elects | update node | | 1000 |
| elects | to set trust | | 1000 |
| election | increase vote | | 5 |
| elects | redeemable vote | | 3 |
| vote | switch vote | | 8 |
| elects | to redeem and defrost | | 2 |
| elects | to extract interest | | 3 |
| recharge mining | extract interest | | 3 |
| transaction | initiation | | 8 |
| transactions | revocation | | 2 |

For example, for an increase of vote transaction, the final transaction number of bytes is 150 bytes according to the parameter size filled in by the user, and the user selects the acceleration multiple of 2 times, then the final handling fee is:

```
(10000 polka * 5 + 150 * 100 polka) * 2 = 130000 polka = 0.0013 PCX
```

- in the initial stage, there are few transactions, the network will not be congested, and the service fee of 1 times can fully meet the needs of users.
- benchmark handling fees and byte handling rates decrease as system performance improves.
- although users need to pay a fee, as long as users normally participate in the node election, on the one hand, they can get system dividends, on the other hand, most of the fee will enter the node bonus pool, and eventually return to users, non-high-frequency operation users can still make money, high-frequency users need to buy another PCX.

### special accounts

* the team's multi-signature account address is XXXX, for continuous development funding support, follow the whole system from 0, the ceiling is 10% of the total circulation.
* the tic's multi-signature account address is XXXX, providing funds for community development from various penalties in the system and 10% of the interest earned by miners without access to credit.