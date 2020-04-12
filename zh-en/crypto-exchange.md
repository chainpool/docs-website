### Initial configuration

ChainX have two trading pairs:

| | trading on | quotation accuracy | single-hop accuracy | opening price (no accuracy) | opening price (with accuracy) |
| ---  | ---      | ---      | ---      | ---              | ---              |
|, 0, |, PCX/BTC, |, 10^9, |, 10^2, |, 1000, |, 0.0001, |
| 1 | SDOT/PCX | 10^4 | 10^2 | 1000 00 | 10.00 |

* quotation precision: system quotation are integer, quotation precision refers to how to convert to user readable decimal quotation.
* single-hop accuracy: generally the initial setting is 100, can be expanded or reduced in the future, disguised affect the user can read the decimal quotation accuracy.
* opening price: the initial price set by the system according to the estimated value has met the initial conversion logic of recharge mining.
* single largest sweep: 10%, users do not hang a large price changes in the market transaction, the purchase price can not be more than 10% of the price, the selling price can not be less than 10% of the price.

### Run the rules

* latest price: the price of the latest transaction, which changes with the development of market matching.
* average price: is the moving average price of the last hour. The system USES this price to vote for the top-up mining.

### Transaction interface: initiate a delegate

Parameters: transaction on ID, type, direction, quote, amount of commission
Judgment: anyone can submit, according to the transaction pair, distinguish currency and assets, determine the amount or amount of the commission less than or equal to the user's available balance, if the quotation is the market, the range can not deviate more than 10%.

If not clinch a deal, will generate increasing Numbers and store the deity, if we can clinch a deal, will, according to the principle of price and time preference, arrange set individually, asset transfers every sale immediately, and update the latest price, buy a price, and sold for a price, at the same time, update user top-up mining, such as the age if an order is fully clinch a deal, will delete the order.

### Transaction interface: undelegate

Parameter: no.
Judgment: only the owner of the pen number corresponding to the delegate can submit

Delete the order, update the buy or sell price, and convert the user's frozen funds into available balances

