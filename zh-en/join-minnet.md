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

### 2. Prepare before starting the node

#### Delete old test network nodes

The ChainX mainnet has been officially launched on May 25, 2019. If you have run testnet nodes before, please delete all testnet data first:

* If the DB storage directory is specified by `-d` or` --base-path` at startup, then delete this directory completely.

* If no path is specified at startup, delete the default DB directory:

    * Linux(ubuntu):

        ```bash
        $ rm -rf $HOME/.local/share/ChainX
        ```

    * macOS:

        ```bash
        $ rm -rf $HOME/Library/Application\ Support/ChainX

#### Prepare the node startup configuration file

Currently ChainX already provides a way to ** start node ** by reading parameters from the configuration file. For the complete startup parameters, please refer to `chainx --help`:

```bash
./chainx --help

ChainX community
Fully Decentralized Interchain Crypto Asset Management on Polkadot

USAGE:
   chainx [FLAGS] [OPTIONS]
   chainx <SUBCOMMAND>

FLAGS:
   ......
......
OPTIONS:
   ......
   ......
```

All commands that appear in help can be configured in the configuration file, we currently provide a configuration file format of `json`. The optional parameters of the `chainx` program are divided into two parts:` [FLAGS] `and` [OPTIONS] `. The example is as follows:


```jsonc
{
    // same as --validator， `./chainx --validator`
    "validator": true,
}
```


- `OPTIONS`

```jsonc
{
    // same --base-path=<ChainX data path>, `./chainx --base-path=<ChainX data store path>`
    "base-path": "<ChainX data store path>",
}
```

It is not recommended to use the command line to directly pass parameters to start the node. For node security, please use the `json` configuration file to start the node. After the configuration file is written, save the configuration file somewhere, ** please make a corresponding security policy **.

For simplicity, the startup configuration file is named `config.json` and placed in the same directory as the chainx binary program.

```bash
$ ls
├── chainx
├── chainx.md5
└── config.json
```

### 3. Start the node

For nodes that are willing to be validators, we strongly recommend running at least two nodes, a synchronization node and a verifier node. In this way, even when there is a problem with the verifier node, the standby synchronization node can be switched to a block-producing node at any time, to avoid the node being punished for not generating blocks on time.

We recommend using the `json` configuration file to start the node, [download configuration file template](https://gist.github.com/liuchengxu/3b3ed4ce027e39fc89b5a5a6c289bfaf):


** !!! Please do not use this file to start the node directly, be sure to refer to the following startup guidelines to modify the configuration file template and run, each parameter will be explained below. **

** Precautions before starting: **

-Since v1.0.2, `chainx` does not print the output information in the terminal by default, but is stored in` log / chainx.log` under the same directory of `chainx`. For details, please refer to` chainx --help `View` --log * `and other related options.

#### 1. Start the synchronization node

```jsonc
{
    "log": "info,runtime=info",    // Log level configuration, if runtime printing is not required, it can be configured as "log": "info, runtime = warn",
    "name": "<Your-Node-Name>",    // The name displayed in the monitoring station https://stats.chainx.org
    "port": 20222,                 // Node's p2p port
    "ws-port": 8087,               // Node's Websocket port
    "rpc-port": 8086,              // Node's RPC port
    "rpc-external": true,          // true means that the RPC port is open to external access, it is recommended that only the service synchronization node is enabled, and the verification node and the synchronization node that does not provide service are recommended to be closed
    "ws-external": true,           // true means that the RPC port is open to external access, it is recommended that only the service synchronization node is enabled, and the verification node and the synchronization node that does not provide service are recommended to be closed
    "base-path": "<Your-DB-Path>", // chainx data storage path
    "other-execution": "NativeElseWasm",
    "syncing-execution": "NativeElseWasm",
    "block-construction-execution": "NativeElseWasm",
    "importing-execution": "NativeElseWasm",
    "pruning": "archive",  // It is strongly recommended to add this configuration and start in archive mode
    "db-cache": 1024,  // Set the cache of the node database, the unit is MB, which is 1GB here, which can be adjusted according to the configuration of your own machine
    "state-cache-size": 2147483648, // Set the node status tree cache, unit B, which is 2GB (2GB = 2 * 1024 * 1024), which can be adjusted according to the configuration of your machine
    "bootnodes": [
        // Fill in the boot node, ChainX has built in some seed nodes, generally do not need to fill in
    ]
}
```

- `name`: Node name displayed in telemetry
- `base-path`: Data storage path, pay attention to the corresponding in step 1 of the startup node

On the other hand, **for users with large storage space**, we currently strongly recommend adding the above `json` file

```bash
{
    "pruning": "archive",  // Start in archive mode
}
```

Please note that if the data directory generated by the node started in this mode, ** must always have this parameter in the future, and cannot be removed. In the same way, if the data directory started with this parameter is not added at startup, it will never be possible to add this parameter in the future.

This `" pruning ":" archive "` means to start the node using ** archive mode **, and it will save the full state tree, otherwise, the state tree before 256 blocks will be cropped by default.

After the configuration file of the startup parameters is prepared, the following command can be used to start the synchronization node: (Note that if `--default-log` is not added, the log is output to` log / chainx.log` by default)

```bash
./chainx --config=$(pwd)/config.json
```
If you want to run in the background, you can use the following command to start:


```bash
nohup ./chainx --config=$(pwd)/config.json > error.log 2>&1 &
```

After the synchronization node is synchronized to the latest height, you can refer to the next section to pause the synchronization node and modify the startup configuration file of the synchronization node, add parameters such as `validator`,` validator-name`, and start the node again to enter the verifier mode.

#### 2. Start the verifier node

##### 1. Node account public key generation and registration

1. If you don't have an account yet, please go to the wallet https://wallet.chainx.org and click Create Account to generate a node account. The **account address** looks like `5HbT8 ... S9yg`.
2. On the voting page of the wallet (the new version of the wallet is on the asset trust page), click on the registered node and set **node name**, such as NodeABC. Note: the name of the node is the `validator-name` that will be used in the startup file later Field.
3. In the candidate node of the voting page, you can see that your node is in **deselected** state. Need at this time

##### 2. Generate the block public key

If you still do not understand the difference between the node account public key and the block public key, please[click](https://github.com/chainx-org/ChainX/wiki/%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5#%E8%8A%82%E7%82%B9%E8%B4%A6%E6%88%B7%E5%85%AC%E9%92%A5%E5%92%8C%E5%87%BA%E5%9D%97%E5%85%AC%E9%92%A5)

The following content believes that the reader has understood the difference between the account public key and the block public key.

ChainX recommends to use `chainx` executable file to generate` keystore` through the following command, that is, to generate the block address public key (session_key):

```bash
# After entering the password, you can disconnect the node
$ ./chainx --keystore-path=<keystore路径> -i --base-path=<数据存放路径> --default-log
Password:
Repeat again:
......
2019-05-15 11:26:50.510 INFO Roles: FULL
######### The downstream printing is the keystore information generated by the node, and the string of public keys is the public key of the block address (session_key)
2019-05-15 11:26:50.514 INFO Generated a new keypair for keystore: 593a11d6d5930ab2e68fa5d07082ba0102fc7740eee38b79b2793d7d34a2442a (5E5hNNEi...)
......
2019-05-15 11:26:50.610 INFO [runtime|xrml_xdex_spot] [add_trading_pair] currency_pair: CurrencyPair: SDOT/PCX, point_precision: 4, tick_precision: 2, price: 100000, online: true
########## You can use <Ctrl-C> to disconnect the node after seeing the downstream printing
2019-05-15 11:26:50.624 INFO Initializing Genesis block/state (state: 0x9499…b6c3, header-hash: 0xdb82…e55d)
....
```
You can stop (ctrl + c or kill) the node after seeing the information generated by the keystore.

The above command will generate a file named ** node block address public key ** under the specified <keystore storage path>, which looks like this: `d41bf5ce34ec4503e633ed0d003356e111186954ce6143d0cfeb6d0a3e51e662`, the file name is the block address public key sessin_key).


** The content in the file is the mnemonic corresponding to this public key. Please note that the mnemonic system is different from the mnemonic system used in the wallet, so it is impossible to import the mnemonic into the wallet to generate the same public Key to use. **

-The keystore mnemonic system is: mnemonic + password ==> private key
-The mnemonic system in the online wallet is: mnemonic ==> private key

Note that to save the following information, you need to use this information when starting the node:

-  `keystore-path`
-  `base-path`
- input `password`,

(Optional) Paste the public key (session_key) of the node's block address into the browser https://scan.chainx.org search box to get the address corresponding to the public key, which will be used to update the block address.

![image](https://user-images.githubusercontent.com/8850248/57749106-4fe2f700-770f-11e9-93e1-50921b6b769c.png)

##### 3. Modify the startup configuration file to start the verifier block node

For the verifier node, we recommend the following configuration:

```jsonc
{
  "validator": true, // Verifier node must be true
  "rpc-external": false, // The verifier node recommends closing the external rpc port
  "ws-external": false, // The verifier node recommends closing the external ws port
  "log": "info,runtime=info",
  "port": 20222,
  "ws-port": 8087,
  "rpc-port": 8086,
  "other-execution": "NativeElseWasm",
  "syncing-execution": "NativeElseWasm",
  "block-construction-execution": "NativeElseWasm",
  "pruning": "archive",  // It is strongly recommended to add this configuration and start in archive mode
  "db-cache": 1024,  // Set the cache of the node database, the unit is MB, which is 1GB here
  "state-cache-size": 2147483648, // Set the node state tree cache, unit B, which is 2GB (2GB = 2 * 1024 * 1024)
  "bootnodes": [],
  "name": "Your-Node-Name",             // The node name displayed in the node browser
  "validator-name": "Your-Validator-Name", // The node name displayed in the node browser
  "base-path": "<The data storage path specified in step 2>",
  "keystore-path": "<The keystore path specified in step 2>",
  "keystore-password": "<The password used in the keystore generated in step 2"
}
```
Some key configuration explanations in the configuration file:

- `name`: Node name displayed in telemetry
- `validator-name`: The name used when registering the node, ** Please note that it must be the same as the name of the node registered in the "Verifier Public Key Generation and Registration" step
- `base-path`: Data storage path, pay attention to the corresponding in step 1 of the startup node
- `keystore-path`: Start the node's keystore path specified in step 1
- `keystore-password`: Start the password entered in step 1 of the node
- `validator`: If set to `true`, then start the verification node, otherwise it is a synchronization section

For nodes with high hard disk performance and large space, ChainX also recommends adding `" pruning ":" archive "` mode to the configuration file to start the archive mode! (Nodes that were not started in archive mode before ignore this)

Next, you can start the node through the command: (Note that if `--default-log` is not added, the log is output to` log / chainx.log` by default)

```bash
./chainx --config=$(pwd)/config.json
```

If you want to run in the background, you can use the command to start

```bash
nohup ./chainx --config=$(pwd)/config.json > error.log 2>&1 &
```

Please observe the log after startup (the default is in `log / chainx.log`):

```bash
2019-05-15 11:37:58.824 INFO load key from keystore. key:<The public key is the public key of the keystore, that is, the public key of the block address (session_key>
2019-05-15 11:37:58.824 INFO Using authority key <The public key is the public key of the keystore, that is, the public key of the block address (session_key)>
```

** Please ensure that the public key in the above log is the public key of the block address (session_key).

###### How to update the block address

On the voting page of the wallet, click Update Node and fill in the public key (session_key) obtained in the first step at the block address. Note that you can fill in the public key directly or from the (optional) step Obtained address:

![image](https://user-images.githubusercontent.com/8850248/57748969-bfa4b200-770e-11e9-843f-c841f3b887e6.png)

** After updating the block address, verify that the block address matches the generated node public key: **
First enter the node details page of the blockchain browser, click on the block address, and then you will see the public key corresponding to the address, check whether it is consistent with the public key of the block address (session_key). If they are consistent, it is correct, otherwise the block address is incorrect.

![image](https://user-images.githubusercontent.com/8850248/57750530-38a70800-7715-11e9-969a-a7277739be6f.png)

Node operation and maintenance as follows：[ChainX Node ](https://github.com/chainx-org/ChainX/wiki/devops)

### 4. Other Works

After confirming that the block node runs successfully, if the node is in the deselected state, you need to click Update Node on the voting page to make the node enter the candidate state:

- Block address
- URL
- Introduction
- Choose "Participate"

Ask the community to vote for the top 30, and every 60 minutes (an integer multiple of 1800 blocks) will conduct a validator election. If the top 30 can become a verification node to participate in the block, if the command line prints `Prepared block for proposing At 6467` and the like, it means that a block is being produced.

** If a node goes offline due to improper node deployment, etc., the system will punish the node prize pool in each dividend cycle **, the penalty for missing a block is 3 times the reward of a block, and the penalty is deducted from the node prize pool. If the prize pool is punished to 0, it will automatically be forced to withdraw. After the node needs to check the deployment, it will update the node to the participating state again and wait for the next round.

The node can become a candidate validator when the node changes, but the qualification of the candidate validator has a minimum total number of votes. The current minimum threshold is 10PCX. When the validator changes normally, if the total number of votes of the candidate validator is less than 10PCX, the candidate will be forcibly withdrawn and the validator will be disqualified.
