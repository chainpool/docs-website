ChainX integration by default the first phase of 5 million DOT the list of investors, the etheric fang contract according to the address as xb59f67a8bff5d8cd03f6ac17265c550ed8f33907 [0] (https://etherscan.io/token/0xb59f67a8bff5d8cd03f6ac17265c550ed8f33907),

## Start the configuration

* balance list: Ethereum addresses and DOT balances for 3,049 investors.
* the carrier of cross-chain remarks is the InputData field in the transaction.

Transaction interface: submit transaction birdge_sdot/push_tx

Parameters: Ethereum address, transaction text, signature
Judgment: anyone can submit, the signature must be correct, and the InputData format of the transaction text meets the requirements.

If the Ethereum user has not received it, the SDOT in the corresponding balance list is issued to the user, and if the node name exists, the recharge channel for the Ethereum user is recorded.

## SDOT relay system

Theory, users can use its DOT Ethereum account to any address by an arbitrary amount of trading, as long as InputData format meet the requirements, which can be a legitimate ChainX mapping, but on the safe side, recommend the user to send its own address 0 amount together since the transfer transactions, and fill the InputData, record transactions ID, after manual to purse relay interface program, apply for relaying.

The relay program can also automatically listen to all investors' transactions and actively submit legitimate mapping transactions to ChainX.