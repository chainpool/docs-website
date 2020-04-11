## Join ChainX Mainnet

<!-- TOC GFM -->

### 0.Fundamental

ChainX is a PoS blockchain, including the basic characteristics of staking.

In the ChainX, We defined the following concepts:

1. Physically running nodes
   The physical running node refers to the actual running node process.The process can be started in two modes, which can be determined by `" validator ": true / false` in the configuration file.
   1. Synchronization node: It is only used to synchronize block data, and there is no block generation function.Generally used to provide rpc service locally, or synchronize block data.
   2. Verifier node: with block generation function
2. Nodes defined on the chain (that is, verification nodes / synchronization nodes / deselection nodes displayed in the ChainX wallet, note: the synchronization node here was previously called a candidate node, but it was renamed as a synchronization node just to facilitate user voting)

Nodes on the chain first need to register as a node on ChainX, everything is subject to the data on the chain

The nodes on the chain are related to staking. The verification nodes and synchronization nodes here do not refer to the nodes in the physical sense, but refer to the state that this address is in staking. But please note that if you want to participate in staking, the physical node must be the verifier node, that is, "validator": true and set "validator-name": "xxx" corresponds to the node name of the node defined on the chain, if Becoming a verification with the ability to generate blocks, you need to ensure that the public key in the keystore is the public key of the block configured on the chain (see below for details):

    1. Verification node: Register as a node. After participating in the election, if the total number of votes of the node reaches the top 30, it will automatically become a verification node, participate in the block generation, and can obtain the profit of each dividend cycle. If the physical node is not the verifier node or the public key configuration of the block is incorrectly configured at this time, it will result in a penalty for withdrawal because the block cannot be generated.
    2. Synchronization node: Register as a node. After the election, if the total number of votes for this node is after 30, it will automatically become a synchronization node. It does not participate in the block generation, but can still obtain the profit of each dividend cycle. It is regarded as a node candidate. After a validator node drops 30, the sync node will keep up with the verifier node and make blocks.
    3. Retired node: After participating in the election, the prize pool will be punished as 0 or because of other penalties, the node will be converted into a retired node. The nominated node has no revenue and does not participate in any staking activities. If you want to participate in staking again, you need to participate in the election.

3. The connection between the physical running node and the defined node on the chain

The physical node and the node on the chain are linked by starting the configuration of the physical node's validator-name, and whether they have the normal right to generate blocks. 

### 1. Download the node program

We provide the following two ways:

* Download the latest version of ChainX binary chainx at https://github.com/chainx-org/ChainX/releases, currently only supports Ubuntu 16.04+ or macOS.
* If it is another system, you can use  [Docker to run ChainX nodes](https://github.com/chainx-org/ChainX/wiki/Docker).



