ChainX modeled on ethereum, boca's pfedsystem, and designed a specific pfedmodel.

### ChainX fee basics:

1. Transaction weight 'weight'
2. Transaction base fee 'transaction_base_fee'
3. Transaction byte fee 'transaction_byte_fee'
4. Trading acceleration 'accelerate'

In ChainX

* 'weight' : the callable method of transaction is given a weight named 'weight', for example, the weight of 'transfer' is 1, and the weight of 'withdraw' is 3. 'weight' represents the complexity, or resources, of the transaction, and the base value of its weight is' transaction_base_fee '. In other words, the amount of weight given to several trades means that the transaction is several times as complex as the base transaction. 'weight' can be adjusted by parliament
* 'transaction_base_fee' : the base value of the transaction weight, that is, it represents the fee size of the weight
* 'transaction_byte_fee' : the transaction fee per byte. Since the transaction takes up a certain amount of space, the larger the transaction is, the more the transaction fee will be
* 'accelerate' : transaction acceleration multiple, when two transactions are the same, the higher the 'accelerate', the higher the rank in the transaction pool, the easier to be packaged, the corresponding commission will also consume the corresponding multiple. This concept is similar to ethereum's 'gas price' and should be selected by the user to provide a numerical value.

The poundage of ### ChainX:

``` bash
Fee = (transaction_base_fee * weight + transaction_byte_fee * len(transaction length)) * accelerate
```

As can be seen from the calculation method, where:

* 'weight', 'transaction_base_fee' and 'transaction_byte_fee' are provided on the ChainX chain
* the transaction length 'len' and 'accelerate' are determined by the transaction sender

### ChainX provides poundage related interface

Currently ChainX provides two RPCS related to poundage computation

1. [chainx_getFeeByCallAndLength] (RPC# chainx_getFeeByCallAndLength)

This interface passes the transaction-encoded function text (note that it is not the transaction body, but the function part of the transaction body), the transaction length, which can be calculated in the ChainX node to get the commission value of ** before ** is accelerated.

2. [chainx_getFeeWeightMap] (RPC# chainx_getFeeWeightMap)

This interface gets the 'weight' list, 'transaction_base_fee' and 'transaction_byte_fee' on the current ChainX. Developers can calculate the fees payable locally based on the above formula according to these three parameters.