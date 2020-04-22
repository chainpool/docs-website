### 安装插件

点击 [Chainx Extension](https://chrome.google.com/webstore/detail/chainx-extension/dffjlgnecfafjfmkknpipapcbgajflge) 安装插件。

> 如果因为网络无法安装插件，可以在我们的[插件发布仓库](https://github.com/chainx-org/chainx-extension-release)下载插件压缩包，并通过 Chrome 的 Load unpacked 来加载插件。

安装完成之后

1. 将网络切换至测试网

   1.	ChainX当前对于测试网默认提供了`wss://testnet.w1.org.cn/ws` 的节点配置
   2.	如使用`ChainX Dev`模式或者在本地跑了测试网的同步节点，可以添加localhost：`ws://localhost:<websocket的端口>`

2. 新建或者导入账户

   流程如下：

   ![image-20191114175210101](../../_media/contract/image-20191114175210101.png '')

3.	在导入钱包界面若选择”导入钱包“，在助记词页面右上角有直接导入私钥的选项。

2. 在浏览器插件中将网络切换到测试网, 合约功能目前只在测试网开启。

### 开始部署并调试

整个过程主要分为 3 个步骤：

1. 上传合约(Upload WASM).
2. 实例化合约, 或者叫部署合约(Deploy)
3. 调用合约

所有步骤可以通过 https://dapps.chainx.org.cn/ 操作，也可以通过 JS-SDK 进行脚本调用。


#### 1. 上传合约


* 打开 [Chainx Wallet](https://dapps.chainx.org.cn/)，并切换到『上传合约』页面：

![image-20191114160022577](../../_media/contract/image-20191114150242636.png '')

* 确保你合约目录的 `target` 目录存在编译好的 `wasm` 文件和 `json` 文件：

![image-20191114160022577](../../_media/contract/image-20191114120447447.png '')

* 点击 UPLOAD WASM，并在表单中上传和填写相应的参数：

![image-20191114160022577](../../_media/contract/image-20191114150517915.png '')

* 点击 CONFIRM 之后，会调起插件对该上传交易进行签名，请输入账户密码并确认签名

* 合约成功部署后显示如下（包含该合约所有的方法）：

![image-20191114160022577](../../_media/contract/image-20191114151322439.png '')

#### 2. 实例化合约（部署合约）

上一步的上传合约，只是简单的将合约代码存储在链上，还没有任何可以操作和调用的功能，所以接下来我们需要将这份合约进行实例化。

* 点击 Deploy 并填写实例化必要的参数：

![image-20191114160022577](../../_media/contract/image-20191114152810561.png '')

调起插件之后，输入密码并确认签名即可实例化合约。

* 合约部署成功之后，页面会自动跳转到『合约』页面，该页面如下图所示：

![image-20191114160022577](../../_media/contract/image-20191114154411579.png '')

* 若链上已经存在这个合约，可点击`ADD EXISTING CONTRACT`按钮，添加一个已经存在的合约。请注意在添加这个合约实例的时候需要同时提供这个合约的`abi.json`，若提供错误的abi，在调用时会造成selector错误，无法调用对应函数。


#### 3. 调用合约方法


![image-20191114160022577](../../_media/contract/image-20191114155829408.png '')

方法执行成功后会在下方的结果区域显示相应的返回数据：


![image-20191114160022577](../../_media/contract/image-20191114160022577.png '')

`total_supply` 与我们初始化合约的时候一致。至此我们的合约上传部署调用就完成了。