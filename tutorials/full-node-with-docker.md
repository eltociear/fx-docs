# Full node with Docker

This guide will explain how to install the `fxcored testnet` or `fxcored master` entrypoint onto your system. With these installed on a server, you can participate in the mainnet or testnet as a [Validator](../validators/validator-setup.md).

## Install f(x)Core

> **You need to **[**install f(x)Core**](installation.md)** before you go further**

### Use Docker

* pull docker images

> if you do not already have docker installed, there will be a prompt for you to install it. Follow the instructions given.

```bash
# For testnet：
  docker pull functionx/fx-core:testnet
# For mainnet：
  ...  
```

* init config

```bash
# For testnet：
  docker run --rm -v ~:/root functionx/fx-core:testnet init fx-zakir
# For mainnet：
  ...  
```

* download genesis (copy and run each line, line by line)

```bash
# For testnet：
sudo wget https://raw.githubusercontent.com/functionx/fx-core/testnet/public/genesis.json -O ~/.fxcore/config/genesis.json
sudo wget https://raw.githubusercontent.com/functionx/fx-core/testnet/public/config.toml -O ~/.fxcore/config/config.toml
sudo wget https://raw.githubusercontent.com/functionx/fx-core/testnet/public/app.toml -O ~/.fxcore/config/app.toml
# For mainnet：  
  ...
```

{% hint style="info" %}
At this stage \*\*BEFORE \*\*starting the node, if you would like to do a fast sync with the snapshot guide, please refer to this [link](use-snapshot.md).

And at this stage, what is important is your validator keys that is stored in a json file for you to do a recovery in the future. For more [information](../f-x-core/setup-node/validator-recovery.md) how to access the files.
{% endhint %}

* run docker

```bash
# For testnet：
  docker run --name fxcore -d --restart=always -p 26656:26656 -p 26657:26657 -p 1317:1317 -p 26660:26660 -v ~:/root functionx/fx-core:testnet start
# For mainnet：
  ... 
```

To check if fxcore is synced:

```bash
curl localhost:26657/status
```

Return:

```bash
{
  "jsonrpc": "2.0",
  "id": -1,
  "result": {
    "node_info": {
      "protocol_version": {
        "p2p": "8",
        "block": "11",
        "app": "0"
      },
      "id": "123868554adafd679f5dc6367bddea39aa5adb94",
      "listen_addr": "tcp://0.0.0.0:26656",
      "network": "fxcore",
      "version": "v0.34.9",
      "channels": "40202122233038606100",
      "moniker": "moniker",
      "other": {
        "tx_index": "on",
        "rpc_address": "tcp://0.0.0.0:26657"
      }
    },
    "sync_info": {
      "latest_block_hash": "239609FE5FD475389C1ACFCEF46DCF6B0343F0C04E43A7968677809C2D489F3F",
      "latest_app_hash": "0D2F1299950E0DE86BFF1CDEEEDE3BA57F7899EF1492A6E6809DF3060164046D",
      "latest_block_height": "810805",
      "latest_block_time": "2021-09-01T07:56:29.166926257Z",
      "earliest_block_hash": "12B0FB286BD34C077CACF97D3D2757B27C49E63FB81E6262399FF11A3C3C002E",
      "earliest_app_hash": "E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855",
      "earliest_block_height": "1",
      "earliest_block_time": "2021-07-12T07:42:23.292429Z",
      "catching_up": false
    },
    "validator_info": {
      "address": "214999E9412502DE8DE13F626F9D32D41C1B5015",
      "pub_key": {
        "type": "tendermint/PubKeyEd25519",
        "value": "fUlPjsYSLi04vwzeNiFObBVabV6TKhB6WB3c65iEayU="
      },
      "voting_power": "0"
    }
  }
```

To ensure that the blocks are synced up with your node, under "sync\_info", "catching\_up value" should be false `"catching_up value": false`. This may take a few hours and your node has to be fully synced up before proceeding to the next step. You may cross reference the latest block you are synced to "sync\_info": "latest\_block\_height" and the latest block height of our Testnet blockchain on our [Testnet blockchain explorer](https://aabbcc-explorer.functionx.io/fxcore/blocks).

### Upgrading Your Node

These instructions are for full nodes that have ran on previous versions of and would like to upgrade to the latest testnet.

#### Reset Data

First, remove the outdated files and reset the data.

```bash
rm $HOME/.fxcore/config/addrbook.json $HOME/.fxcore/config/genesis.json
fxcored unsafe-reset-all
```

Your node is now in a pristine state while keeping the original `priv_validator.json` and `config.toml`. If you had any sentry nodes or full nodes setup before, your node will still try to connect to them, but may fail if they haven't also been upgraded.

> Make sure that every node has a unique `priv_validator.json`. Do not copy the `priv_validator.json` from an old node to multiple new nodes. Running two nodes with the same `priv_validator.json` will cause you to double sign.
