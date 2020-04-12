### Asset List

| short | chain | name | precision | maximum circulation |
| ---- | ----     | ----            | ---- | ----       |
| PCX | ChainX | Polkadot ChainX | 8 | 21 million |
| BTC | Bitcoin | x-btc | 8 | 21 million |
| SDOT | Ethereum | Shadow DOT | 3 | 10 million |

User asset status

The status of all kinds of assets of the user is divided into: available balance, frozen vote, frozen redemption, frozen withdrawal, frozen currency transaction, the total assets of the user is the sum of these asset status.

### Cross-chain Recharge

After the user has created his/her own ChainX address, if the off-chain asset needs to be recharged into the ChainX system across the chain, the corresponding relationship between the off-chain account and the ChainX account needs to be established. In order to issue a mapping currency to a ChainX user, the user has to top up multiple addresses in the original chain.

* the cross-chain remark format is' user ChainX address 'or' user ChainX address @ node name '. If the node name is filled in, it will be recorded as' recharge channel '.
* BTC users need to carry OP_Return in the transaction, SDOT users need to carry InputData in the transaction.
* after the user has been bound, the ChainX will record the binding relationship, and the next time the same original chain address is transferred directly, the previous bound ChainX address will be used by default.
* a ChainX address may bind multiple original chain addresses, but the top up channel for a ChainX address defaults to the node name that was carried the first time it was bound.
* total issuance is recorded for all types of cross-chain assets.

### Transaction interface

#### Xassets/Transfer

* parameters: receive accounts, assets, amounts, notes
* determination: the amount needs to be less than your available balance, and the note needs to be less than 64 bytes

#### xassets/withdraw

* parameters: assets, original chain address, amount, remarks
* judgment: the asset can only be x-btc at present, the original chain address needs to be a legitimate Bitcoin address, the amount is less than or equal to the user's BTC available balance, note needs to be less than 64 bytes.

After the user applies for cash withdrawal, they need to wait for the trust node to review, and periodically issue a summary of users' cash withdrawal requests, issue multiple withdrawals, sign them one by one, and wait for broadcasting to the bitcoin network. After several blocks of confirmation, users' cash withdrawal requests will be deleted, and the total circulation of x-btc will also be reduced.