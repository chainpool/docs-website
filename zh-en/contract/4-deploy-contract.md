## Deploying Your Contract

### Install the plugin

Click [Chainx Extension](https://chrome.google.com/webstore/detail/chainx-extension/dffjlgnecfafjfmkknpipapcbgajflge) to install the plugin.

> If the plugin cannot be installed because of the network, you can download the plugin compressed package in our [plugin distribution repository](https://github.com/chainx-org/chainx-extension-release) and load it in Chrome browser (please see at readme file on this repository).

After the installation is complete:

1. Switch network to test network

   1.	ChainX currently provides `wss://testnet.w1.org.cn/ws` as default node configuration
   2.	If you are using `ChainX Dev Mode`, you have to add localhost:`ws://localhost:<websocket>`

2. Create or import account

   ![image-20191114175210101](../../_media/contract/image-20191114175210101.png '')

3. If you select "Import Wallet", there is an option to directly import the private key in the upper right corner of the mnemonic page.


### Deployment and debug

The whole process is mainly divided into 3 steps:

1. Upload of the contract on chain (Upload WASM file)
2. Instantiate of the contract (or deploy)
3. Call contract functions

All steps can be performed by UI (https://dapps.chainx.org.cn/), or by JS-SDK script.

#### 1. Upload Contract


1. Open [Chainx Wallet](https://dapps.chainx.org.cn/) and switch to "Contract" page and "Contract Code" tab:

![image-20191114160022577](../../_media/contract/image-20191114150242636.png '')

2. Make sure that the compiled `wasm` file and `json` file exist in the `target` directory of your contract directory:

![image-20191114160022577](../../_media/contract/image-20191114120447447.png '')

3. Click on "Upload WASM"，and fill in the corrisponding fields in the form:

![image-20191114160022577](../../_media/contract/image-20191114150517915.png '')

4. After clicking on "Confirm", the plugin will be called to sign the transaction. Please enter the account password and confirm the signature

5. After the contract is successfully uploaded on chain, it is displayed as follow (including all methods of the contract):

![image-20191114160022577](../../_media/contract/image-20191114151322439.png '')

#### 2. Instantiate of the contract

In the previous step about uploading of the contract, we simply stored the contract code on the chain and did not have any functions that can be called, so we need to instantiate this contract.

* Click "Deploy" and fill in the necessary fields for instantiation:

![image-20191114160022577](../../_media/contract/image-20191114152810561.png '')

After calling up the plugin, enter the password account and confirm the signature to instantiate the contract.

* After the contract is successfully deployed, the UI will automatically jump to the "Contract" tab, as shown in the following figure:：

![image-20191114160022577](../../_media/contract/image-20191114154411579.png '')

* If the contract is already deployed, click the "Add existing contract" button to add an existing contract. Please note that when adding an existing contract, you need to provide the contract's `abi.json`. If you provide the wrong abi, it will cause a selector error when calling, and the corresponding function cannot be called.

#### 3.  Call contract functions


![image-20191114160022577](../../_media/contract/image-20191114155829408.png '')

After the function is successfully executed, the corresponding returned data is displayed in the result area:


![image-20191114160022577](../../_media/contract/image-20191114160022577.png '')

`total_supply` is the same as we started  init the contract.At this point our contract upload deployment call is complete.

