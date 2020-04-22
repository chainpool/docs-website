ChainX主网与2019年5月25日启动.

Useful links:

- 在线钱包: https://wallet.chainx.org
- 区块浏览器: https://scan.chainx.org
- Telemetry: https://stats.chainx.org/#/ChainX and https://telemetry.polkadot.io/#list/ChainX

## 1. 下载ChainX二进制软件包

目前，ChainX尚未开源。您目前有两种获取ChainX程序的方法：

- Release版本二进制文件位于 https://github.com/chainx-org/ChainX/releases/tag/v1.0.0
- using docker(WIP)

## 2. 运行同步节点

强烈建议通过传递`--config`将JSON用作chainx配置文件。直接从命令行传递参数是不安全的，尤其是对于`--key`选项。


查看 `FLAGS` 和 `OPTIONS` via `chainx --help`.

`config.json`:

```jsonc
{
    "log": "info,runtime=info",    // log level, set to "info,runtime=warn" if you don't want the runtime logging
    "name": "<Your-Node-Name>",    // name displayed at https://stats.chainx.org/#/ChainX and https://telemetry.polkadot.io/#list/ChainX
    "port": 20222,                 // p2p port
    "ws-port": 8087,               // Websocket port
    "rpc-port": 8086,              // RPC port
    "rpc-external": true,          // accessible by external
    "ws-external": true,           // accessible by external
    "base-path": "<Your-DB-Path>", // where is the DB stored
    "other-execution": "NativeElseWasm",
    "syncing-execution": "NativeElseWasm",
    "block-construction-execution": "NativeElseWasm",
    "importing-execution": "NativeElseWasm",
    "bootnodes": [
    ]
}
```

根据上面的配置运行一个同步节点：

```bash
./chainx --config=$(pwd)/config.json
```

然后您可以在以下位置看到您的节点 https://stats.chainx.org/#/ChainX 和 https://telemetry.polkadot.io/#list/ChainX .

## 3. 运行一个验证人节点

在运行验证器节点之前，您应该确保同步节点已完全同步。

### 1. 生成 session key

每个验证器都有两个密钥：

- `validator_key`: 节点账户的秘钥
- `session_key`:  出块节点秘钥，你可以根据需要进行更新

通过交互式创建密钥库来生成`session_key`：

```bash
$ ./chainx --keystore-path=<keystore-path> -i --base-path=<DB-path>
Password:
Repeat again:
......
2019-05-15 11:26:50.510 INFO Roles: FULL
######### This indicates that you have generated the keystore successfully.
2019-05-15 11:26:50.514 INFO Generated a new keypair for keystore: 593a11d6d5930ab2e68fa5d07082ba0102fc7740eee38b79b2793d7d34a2442a (5E5hNNEi...)
......
2019-05-15 11:26:50.610 INFO [runtime|xrml_xdex_spot] [add_trading_pair] currency_pair: CurrencyPair: SDOT/PCX, point_precision: 4, tick_precision: 2, price: 100000, online: true
########## You can stop the process by <Ctrl-C> when you see this line.
2019-05-15 11:26:50.624 INFO Initializing Genesis block/state (state: 0x9499…b6c3, header-hash: 0xdb82…e55d)
....
```

确保您已保存以下信息：

- `keystore-path`
- `base-path`
- 输入的密码

### 2. 修改`config.json`以启用验证人模式

现在你需要添加 `validator` 和 `validator-name` 来运行验证节点

```jsonc
{
  "validator": true,                       // true for validator mode
  "validator-name": "Your-Validator-Name", // name used when registering your node in https://wallet.chainx.org
  "name": "Your-Node-Name",                // any nickname displayed at https://stats.chainx.org/#/ChainX and https://telemetry.polkadot.io/#list/ChainX
  "rpc-external": false,                   // false is recommended for validator node
  "ws-external": false,                    // false is recommended for validator node
  "log": "info,runtime=info",
  "port": 20222,
  "ws-port": 8087,
  "rpc-port": 8086,
  "other-execution": "NativeElseWasm",
  "syncing-execution": "NativeElseWasm",
  "block-construction-execution": "NativeElseWasm",
  "base-path": "<DB-path in the last step>",
  "keystore-path": "<keystore-path in the last step>",
  "keystore-password": "<password you inputed in the latest step>",
  "bootnodes": []
}
```

注意:

- `name`: 可以是任何你喜欢的，仅用作展示 
- `validator-name`: 这是您在注册节点时使用的名称 https://wallet.chainx.org

```bash
./chainx --config=$(pwd)/config.json
```
