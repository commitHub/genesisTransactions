# core-1 
> This chain is a main-net
 
> FINAL GENESIS PUBLISHED
 
> FINAL PEERS PUBLISHED

1st main-net for persistenceCore application.

## Hardware Requirements
* **Minimal**
    * 4 GB RAM
    * 200 GB SSD
    * x2 CPU
* **Recommended**
    * 8 GB RAM
    * 500 GB SSD
    * x4 CPU

> NOTE: low endurance(tbw) ssd are not supported

## Operating System
* Linux/Windows/MacOS(x86)
* **Recommended**
    * Linux(x86_64)

## Installation Steps
>Prerequisite: go1.19.3+ required. [ref](https://golang.org/doc/install)

>Prerequisite: git. [ref](https://github.com/git/git)

>Optional requirement: GNU make. [ref](https://www.gnu.org/software/make/manual/html_node/index.html)

* Clone git repository
```shell
git clone https://github.com/persistenceOne/persistenceCore.git
```
* Checkout release tag
```shell
cd persistenceCore
git fetch --tags
git checkout v0.1.3
```
* Install
```shell
make install
```
* Verify version
```
persistenceCore version
```
> The current version is b0aec61

### Generate keys

`persistenceCore keys add [key_name]`

or

`persistenceCore keys add [key_name] --recover` to regenerate keys with your [BIP39](https://github.com/bitcoin/bips/tree/master/bip-0039) mnemonic


## Validator setup

### Before genesis: CLOSED please refer to section [Post Genesis](#post-genesis)

* [Install](#installation-steps) persistence core application
* Initialize node
```shell
persistenceCore init {{NODE_NAME}} --chain-id core-1
persistenceCore add-genesis-account {{KEY_NAME}} 1000000000uxprt
persistenceCore gentx {{KEY_NAME}} 1000000000uxprt \
--chain-id core-1 \
--moniker="{{VALIDATOR_NAME}}" \
--commission-max-change-rate=0.01 \
--commission-max-rate=1.0 \
--commission-rate=0.07 \
--details="XXXXXXXX" \
--security-contact="XXXXXXXX" \
--website="XXXXXXXX"
```
* Copy the contents of `${HOME}/.persistenceCore/config/gentx/gentx-XXXXXXXX.json`.
* Fork the [repository](https://github.com/persistenceOne/genesisTransactions)
* Create a file `gentx-{{VALIDATOR_NAME}}.json` under the core-1/gentxs folder in the forked repo, paste the copied text into the file. Find reference file gentx-examplexxxxxxxx.json in the same folder.
* Run `persistenceCore tendermint show-node-id` and copy your nodeID.
* Run `ifconfig` or `curl ipinfo.io/ip` and copy your publicly reachable IP address.
* Create a file `peers-{{VALIDATOR_NAME}}.json` under the core-1/peers folder in the forked repo, paste the copied text from the last two steps into the file. Find reference file peers-examplexxxxxxxx.json in the same folder.
* Create a Pull Request to the `master` branch of the [repository](https://github.com/persistenceOne/genesisTransactions)
>**NOTE:** the Pull Request will be merged by the maintainers to confirm the inclusion of the validator at the genesis. The final genesis file will be published under the file core-1/final_genesis.json.
* Replace the contents of your `${HOME}/.persistenceCore/config/genesis.json` with that of core-1/final_genesis.json.
* Add `persistent_peers` or `seeds` in `${HOME}/.persistenceCore/config/config.toml` from core-1/final_peers.json.
* Start node
```shell
persistenceCore start
```

### Post genesis

* [Install](#installation-steps) persistence core application
* Initialize node
```shell
persistenceCore init {{NODE_NAME}} --chain-id core-1
```
* Replace the contents of your `${HOME}/.persistenceCore/config/genesis.json` with that of core-1/final_genesis.json from the `master` branch of [repository](https://github.com/persistenceOne/genesisTransactions).
* Verify checksum `sha265sum genesis.json` matches `673d30abd133a13210bf271d8a52aabc3f1b12c0864f543f4313f7f9589bdb53`
* Inside file `${HOME}/.persistenceCore/config/config.toml`, 
  * set `seeds` to `"449a0f1b7dafc142cf23a1f6166bbbf035edfb10@13.232.85.66:26656,5b27a6d4cf33909c0e5b217789e7455e261941d1@15.223.104.135:26656"`.
  * If your node has a public ip, set it in `external_address = "tcp://<public-ip>:26656"`, else leave the filed empty.
* Set `minimum-gas-prices` in `${HOME}/.persistenceCore/config/app.toml` with the minimum price you want (example `0.005uxprt`) for the security of the network.
* Start node
```shell
persistenceCore start
```
* Acquire $XPRT tokens to self delegate to your validator node. Minimum 1 XPRT is require to become a validator. You must send your XPRT to the address created in the Generate Keys step previosuly.
* Wait for the blockchain to sync. You can check the sync status using the command
```
curl http://localhost:26657/status sync_info "catching_up": false
```
Once `"catching_up"` is `false`, the sync is complete. If this is a production validator, we recommend syncing from genesis to ensure security. However, for dev purposes, you can achieve a faster sync using snapshots. The latest snapshot is available at  https://tendermint-snapshots.s3.ap-southeast-1.amazonaws.com/persistence/data.tar.lz4 .
You will need to download this file, and unzip it in `~/.persistenceCore/data` using the command `lz4 -d data.tar.lz4 | tar -xv`. Remove the db files that are there currently.
You can unpack this in flight with
```
curl -sSL https://tendermint-snapshots.s3.ap-southeast-1.amazonaws.com/persistence/data.tar.lz4 | tar -I lz4 -xf -
```
* Send a create-validator transaction
```
persistenceCore tx staking create-validator \
--from {{KEY_NAME}} \
--amount XXXXXXXXuxprt \
--pubkey "$(persistenceCore tendermint show-validator)" \
--chain-id core-1 \
--moniker="{{VALIDATOR_NAME}}" \
--commission-max-change-rate=0.01 \
--commission-max-rate=1.0 \
--commission-rate=0.07 \
--min-self-delegation="1" \
--details="XXXXXXXX" \
--security-contact="XXXXXXXX" \
--website="XXXXXXXX"
```
## Version
This chain is currently running on persistenceCore [v0.1.3](https://github.com/persistenceOne/persistenceCore/releases/tag/v0.1.3)
Commit Hash: a555ac81cd3ab856d2d5305414e8a00d24d2043c
>Note: If your node is running on an older version of the application, please update it to this version at the earliest to avoid being exposed to security vulnerabilities /defects.

## Binary 
The binary can be downloaded from [here](https://github.com/persistenceOne/persistenceCore/releases/tag/v0.1.3).

## Explorer
The explorer for this chain will hosted [here](https://explorer.persistence.one) after the chain goes live.

## Wallet
The wallet application for this chain is hosted [here](https://wallet.persistence.one).

## Genesis Time
The genesis transactions sent before 1200HRS UTC 30th March 2021 will be used to publish the final_genesis.json at 1300HRS UTC 30th March 2021. The genesis will start immediately after as soon as a quorum is reached.
