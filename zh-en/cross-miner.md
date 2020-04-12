## Dynamic cross-chain mining model

As a PoS system, the security of ChainX relies on PCX mortgaged by users. The more PCX mortgaged, the more secure the system is. At the same time, because ChainX is dedicated to being a gateway for cross-chain assets, another value support of ChainX is that the more cross-chain assets it connects to, the greater the value. In addition, because the original assets and the cross-chain assets participate in the asset mining competitively, they are an interdependent and competitive relationship.

In order to avoid the impact of a large number of cross-chain assets flooding into the system in a short period of time at the initial stage of the system, the mining of primary assets and cross-chain assets adopts the dynamic mining model. When the growth rate of cross-chain assets is too fast, the fixed dividend ratio is used to allocate the cross-chain assets and primary assets.

At present, the ratio of cross-chain assets to PCX voting mining power limit is 1:1, which can be adjusted by community voting, that is, the mining power limit of all cross-chain assets is set at 50%, to ensure that PCX voting mining power ratio is greater than or equal to 50%, at least half of PCX will be issued to PCX holders every day.

### Symbol Table

The symbol | means
: - | : -
Power<sub>total</sub> | net mining Power
Power<sub>real</sub> | net real mining calculation
Power<sub>virtual</sub> | full network virtual mining calculation
R<sub>total</sub> | the total number of shares issued per dividend cycle
R<sub>real</sub> | each profit sharing cycle the net real computing power of the total PCX
R<sub>virtual</sub> | every bonus cycle the total amount of PCX divided by the network virtual computing power
S<sup> I </sup><sub>active</sub>
Staked<sub>active</sub> | all states are the sum of the total votes of the participating nodes
C | a cross-chain asset, c ∈ (x-btc, l-btc, s-dot... )
Total virtual Power of Power<sub>c</sub> | cross-chain asset c
Amount<sub>c</sub> | cross chain asset c,
Price<sub>c</sub> | unit cross chain asset c for PCX Price
Discount<sub>c</sub> | cross-chain assets c initial Discount on a single asset
UbiquitousDiscount | all across dynamic competitive discount chain assets
FinalDiscount<sub>c</sub> | cross-chain asset c final power discount

### Formula for calculating force correlation

- Total net computing power is equal to the sum of real net computing power and virtual net computing power:

Power<sub>total</sub> = Power<sub>real</sub> + Power<sub>virtual</sub>

- All states are the sum of the total votes of the selected nodes:

Power<sub>real</sub> = Staked<sub>active</sub> = sum (I<sup> I< /sup><sub>active</sub>), I ∈ node list

- The virtual computing power of the whole network is the sum of the virtual computing power corresponding to each cross-chain asset:

Power < sub > virtual < / sub > = sum (Power < sub > c < / sub >), c ∈ (X - BTC, L - BTC, S - DOT,... )

Power<sub>c</sub> = Amount<sub>c</sub> * Price<sub>c</sub> * Discount<sub>c</sub>

For c ∈ (x-btc, l-btc):

-price <sub> x-btc </sub> is calculated based on the average transaction Price of the DEX on the chain in the last hour

-discount <sub> X-BTC </sub> is currently 10%

-l -BTC has no trading pairs. The current calculation force is the same as that of x-btc.

For c ∈ (s-dot):

-price <sub> s-dot </sub> is a fixed 0.1pcx

-discount <sub> S-DOT </sub> is 10%.

- total distribution of all forces issued per dividend cycle:

R<sub>total</sub> = R<sub>real</sub> + R<sub>virtual</sub>

- Final power discount of cross-chain asset c based on market value:

FinalDiscount < sub > c < / sub > = Discount < sub > c < / sub > * UbiquitousDiscount

### Dynamic computational force competition

Currently, the upper limit of mining Power ratio for all cross-chain assets is 50%, that is, Power<sub>virtual</sub> : Power<sub>real</sub> = 1:1. If Power<sub>virtual</sub> > Power<sub>real</sub>, then the upper limit mining Power rule takes effect.

- when Power<sub>virtual</sub> <= Power<sub>real</sub>,

-r <sub>real</sub> = Power<sub>real</sub> / Power<sub>total</sub> * R<sub>total</sub>

-r <sub>virtual</sub> = Power<sub>virtual</sub> / Power<sub>total</sub> * R<sub>total</sub>

- UbiquitousDiscount = 1

- when Power<sub>virtual</sub> > Power<sub>real</sub>,

- R < sub > real < / sub > = 1 / (1 + 1) * R < sub > total < / sub > = 50% * R < sub > total < / sub >

- R < sub > virtual < / sub > = 1 / (1 + 1) * R < sub > total < / sub > = 50% * R < sub > total < / sub >

- UbiquitousDiscount = Power < sub > real < / sub > / Power < sub > virtual < / sub >

After the total reward of the two types of assets is calculated dynamically according to the cross-chain assets and voting assets, the reward of a single node is calculated according to the proportion of each real and virtual node in its category, and then the return of a single user is calculated according to the voting age. When the total number of all cross-chain assets surges to the upper limit, another total discount for all cross-chain assets is imposed in the case that there is always a single asset discount for each cross-chain asset.

### Recharge bonus

Since version v1.0.3, the top-up bonus of all cross-chain assets is no longer distributed according to the proportion of accumulated ticket age during the confirmation period of cross-chain assets, but directly distributed to the cross-chain users 0.001 PCX from the bonus pool of cross-chain assets.

#### BTC

When ~~BTC users recharge across the chain, they need to wait for BTC transaction confirmation (wait for 6 BTC blocks) before ChainX issues x-btc to the corresponding recharge address in the chain. For each BTC recharge, we will calculate the accumulated recharge age of waiting for confirmation according to the user's BTC recharge amount, and issue corresponding recharge reward to the user from the BTC bonus pool. The specific calculation method is as follows: ~~

~~ recharge bonus = (recharge amount * BTC confirmation time)/total BTC age * BTC bonus pool amount ~~

#### SDOT

~~DOT users do not receive top-up rewards for mapping, but will participate in subsequent top-up mining. ~ ~

### Recharge mining interest

BTC and SDOT are equivalent to two virtual nodes, and their "total votes" are equivalent to their own total circulation, which is proportional to the total votes of the following real voting nodes, and the PCX issued in each cycle is equally distributed. The reward for each cycle is sent to a special account with no one knowing the private key, which ACTS as the prize pool for the virtual node, and the calculation logic of the prize pool account is hash (currency name). The pending interest of the user in the virtual node is not automatically allocated, but calculated when the user manually raises the interest.

#### Formula for calculating top-up interest

The formula for calculating age is the same as that for voting.

Each virtual node has the following attributes: total currency age, total currency age update height, which is updated when the circulation of each asset changes, such as recharge and withdrawal confirmation:

` ` `
Total age = old age + old circulation * (current height - old age update height)

Total age update height = current height
` ` `

Users have the following attributes in the virtual node: the user's age and the updated height of the user's age, which are updated when the amount of the asset held by each user changes, such as transfer, coin transaction and withdrawal confirmation:

` ` `
User age = old user age + old user holdings * (current height - old user age update height)

User age update height = current height
` ` `

Then, if the user holds some asset, the virtual node and the user's currency age information can be obtained first, and then the pending interest can be calculated in the front end in real time according to the current height. The calculation logic is as follows:

` ` `
Latest total age = total age + circulation * (current height - total age update height)

Latest user age = user age + holding * (current height - updated user age height)

Total pending interest = (latest user age/latest total age) * amount of prize pool
` ` `

#### Top-up interest distribution

First of all, according to the above calculation formula of top-up interest, the total amount of user's top-up interest is obtained. The actual distribution of user's interest is as follows:

```
User pending interest = total pending interest * 90%

Channel or council pending interest = total pending interest * 10%.
```

When users recharge across the chain, there is an optional recharge channel, which is a node in the ChainX.

- In order to reward the recharge channel, when the user carries out the interest raising operation, the user will first check the recharge channel. If it exists, 10% of the total outstanding interest will be paid to the channel; if it does not exist, it will be paid to the parliamentary account as the community development fund.
- Deduct 10% of the reward channel, and the remaining 90% of the total outstanding interest is the interest actually available to the top-up user.