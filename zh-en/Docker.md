## Run a Docker ChainX Node

ChainX main network mirroring:[chainxorg/chainx:v1.0.2](https://hub.docker.com/r/chainxorg/chainx/tags).

### 1. Generate node keystore

The purpose of this step is to generate the node block authoring address.

```bash
$ docker run -it --rm -v $(pwd)/data:/data -v $(pwd)/log:/log -v $(pwd)/keystore:/keystore chainxorg/chainx:v1.0.2 chainx -i --keystore-path=/keystore --base-path=/data --log-dir=/log
Password:
Repeat again:
...
##### After entering the password, use <Ctrl-C> to exit
```

- The above command will prompt for a password, which is the password of the node keystore.

- After entering the password, a folder named `keystore` will be generated in the current directory, and there will be a file named node public key under the folder. This public key is the corresponding node address of the node block authoring we want to use. 

```bash
$ ls keystore
f1a10ac84641d72074f89c8b4dcaa10ab2a8e982921a81c292f2839f9bf6080f
```

Paste the public key into the browser, you can see the address corresponding to the node's public key.

### 2. Start synchronization node

First prepare a configuration file for node startup `config.json`:

```json
{
    "validator": false,
    "rpc-external": true,
    "ws-external": true,
    "log": "info,runtime=info",
    "name": "<Your-Name>",
    "port": 20222,
    "ws-port": 8087,
    "rpc-port": 8086,
    "base-path": "/data",
    "log-dir": "/log",
    "keystore-path": "/keystore",
    "keystore-password": "<Your-Keystore-Password>",
    "other-execution": "NativeElseWasm",
    "syncing-execution": "NativeElseWasm",
    "block-construction-execution": "NativeElseWasm",
    "bootnodes": []
}
```

- `keystore-password`: Fill in the Password entered in the previous step.

Start synchronization node Docker：

```bash
$ docker run -d --restart always --name chainx -p 8087:8087 -p 8086:8086 -p 20222:20222 -v $(pwd)/data:/data -v $(pwd)/log:/log -v $(pwd)/keystore:/keystore -v $(pwd)/config.json:/config.json chainxorg/chainx:v1.0.2 chainx --config=/config.json
```

View Docker logs:

```bash
# some partial error information, etc.
$ docker logs -f chainx

# Runtime log
$ tail -f log/chainx.log
```

### 3. Register and update the node to enter the candidacy state

- First register the node, the node name filled in is the `validator-name` used when starting the verification node.

- After registering a node, it will be deselected by default. Select the update node and use the node to enter the candidacy state. Only the node that is in the candidacy state and the total number of votes is not 0 will have the opportunity to participate in the verification node election.

- Note that the block-out address filled in when updating the node is the node-out address generated in step 1.

### 4. Start validator node

After the synchronization node is fully synchronized, modify `config.json` to add the necessary information to start the verifier node, and then restart Docker to enter the verifier mode.

modify `config.json`:

- Set `validator` to true,
- Add the `validator-name` field, and` validator-name` is the name filled in when registering the node

```json
{
    "validator": true,
    "validator-name": "<Your-Valitor-Name>",
    "rpc-external": true,
    "ws-external": true,
    "log": "info,runtime=info",
    "name": "<Your-Name>",
    "port": 20222,
    "ws-port": 8087,
    "rpc-port": 8086,
    "base-path": "/data",
    "log-dir": "/log",
    "keystore-path": "/keystore",
    "keystore-password": "<Your-Keystore-Password>",
    "other-execution": "NativeElseWasm",
    "syncing-execution": "NativeElseWasm",
    "block-construction-execution": "NativeElseWasm",
    "bootnodes": []
}
```

Then restart chainx container:

```bash
$ docker restart chainx
```
