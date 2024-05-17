---
description: >-
  The new version of Junction, a cutting-edge blockchain platform designed for
  secure and efficient data management.
---

# 🟩 Airchains

## 1. Information

Website: [https://www.airchains.io](https://www.airchains.io)

Twitter: [https://twitter.com/airchains\_io](https://twitter.com/airchains\_io)

Discord: [https://discord.gg/airchains](https://discord.gg/airchains)

**Faucet on Discord**

## 2. Service

* **Explorer**: [https://explorer.anonid.top/Airchains](https://explorer.anonid.top/Airchains)
* **API**: [https://airchains-api.anonid.top](https://airchains-api.anonid.top)
* **RPC**: [https://airchains-rpc.anonid.top](https://airchains-rpc.anonid.top)

***

## 3. Hardware requirement

### **3.1. Recommended Hardware**

{% code overflow="wrap" fullWidth="false" %}
```
CPU: 4 Cores, RAM: 8GB, Storage: 40GB NVMe SSD
```
{% endcode %}

### 3.2. Minumum Hardware

{% code overflow="wrap" %}
```
CPU: 2 Cores, RAM: 4GB, Storage: 40GB SSD
```
{% endcode %}

### 3.3. OS:

```
Ubuntu 22.04 x86
```

## 4. Guide to Install

### 4.1. Install Go

```sh
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl https://dl.google.com/go/go1.21.8.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
go version
```

### 4.2. Insstall Airchains

```shell
cd $HOME
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond
chmod +x junctiond
sudo mv junctiond /usr/local/bin/
cd $HOME
junctiond version
```

### 4.3. Set chain id and  chain name

Change your <mark style="color:red;">**\<Change-Name>**</mark>

```bash
junctiond config chain-id junction
junctiond init <Change-Name> --chain-id junction
```

### 4.4. Download Genesis & addressbook

```sh
curl -Ls https://snap.anonid.top/config/airchains/genesis.json > $HOME/.junction/config/genesis.json 
curl -Ls https://snap.anonid.top/config/airchains/addrbook.json > $HOME/.junction/config/addrbook.json
```

### 4.5. **Create Service and enable startup**

```sh
sudo tee /etc/systemd/system/junctiond.service > /dev/null <<EOF
[Unit]
Description=junctiond
After=network-online.target
[Service]
User=root
ExecStart=$(which junctiond) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable junctiond
```

### 4.6. Download Snapshort (Updated every day)

{% code overflow="wrap" %}
```bash
junctiond tendermint unsafe-reset-all --home ~/.junction/ --keep-addr-book

wget -O- https://snap.anonid.top/snaps%2Fairchains.snap.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.junction
```
{% endcode %}

## 5. Create or import wallet

### 5.1. Add New Wallet Key&#x20;

```bash
junctiond keys add wallet
```

With "**wallet**" is your wallet name. To easy follow this guide, keep it if you don't want to change.

<mark style="color:red;">**Important:**</mark> <mark style="color:red;">**Save this seed, if you lost it, you can't recovery your wallet**</mark>

### 5.2. Or Recover existing key

```bash
junctiond keys add wallet --recover
```

### 5.3. List All Keys

```bash
junctiond keys list
```

### 5.4 Query Wallet Balance

```bash
junctiond q bank balances $(junctiond keys show wallet -a)
```

With "**wallet**" is your wallet name create or recovery at [#id-5.1.-add-new-wallet-key](airchains.md#id-5.1.-add-new-wallet-key "mention")or [#id-5.2.-or-recover-existing-key](airchains.md#id-5.2.-or-recover-existing-key "mention").

## 6. Check sync status

If it show "**false**", it mean **no need** to wait node sync data or node is synced

If it show "**true**", please wait it some minute (because near to recreate snapshoot - about 6 hour). After status is synced (**false**) you can go to next step. Please be patient, sync time will depend on your server configuration and network speed.

```
junctiond status 2>&1 | jq .sync_info
```

## 7. Create Validator

### **7.1. Obtain your validator public key by running the following command:**

```bash
junctiond comet show-validator
```

The output will be same to this but with a **different key:**

{"@type":"/cosmos.crypto.ed25519.PubKey","key":"_**uyeP+tevNbAm+NYH5pdLfhYnGZyh3hBHD8nGpC2sI14=**_"}

### 7.2. Create a file named **`validator.json`** in **$HOME** by command**:**

```
nano $HOME/validator.json
```

Change your info, include **pubkey, moniker, indentity, website, security, details** and **Save** them

* **Moniker**<mark style="color:red;">\*</mark>: Your validator name (node name) - **Required**
* **Identiry**: Your [keybase.io](https://keybase.io) ID (64-bit)
* **Website**: Your website (if you have)
* **Security**: Normaly is your email
* **Details**: Your slogan to show in validator page

```json
{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"uyeP+tevNbAm+NYH5pdLfhYnGZyh3hBHD8nGpC2sI14="},
	"amount": "1000000amf",
	"moniker": "<validator-name>",
	"identity": "optional identity signature (ex. UPort or Keybase)",
	"website": "validator's (optional) website",
	"security": "validator's (optional) security contact email",
	"details": "validator's (optional) details",
	"commission-rate": "0.1",
	"commission-max-rate": "0.2",
	"commission-max-change-rate": "0.01",
	"min-self-delegation": "1"
}
```

### **7.3. Submit the transaction to create the validator:**

{% code overflow="wrap" %}
```bash
junctiond tx staking create-validator validator.json --from wallet --chain-id junction --fees 5000amf -y
```
{% endcode %}

When command complete, you should copy **TXHash** and check transactions at [https://explorer.anonid.top/Airchains/tx](https://explorer.anonid.top/Airchains/tx) (copy hash to find or paste it after click to search icon adn enter) to verify this transactions  is <mark style="color:green;">success</mark> or <mark style="color:red;">failed</mark> and then check your validator name in staking page of explorer at [https://explorer.anonid.top/Airchains/staking](https://explorer.anonid.top/Airchains/staking). Remember check in **Inactive** tab, because at now, only 100 top validator can be show in **Active** tab.

### 7.4. Faucet

Join **Airchains Discord** server at [https://discord.gg/airchains](https://discord.gg/airchains)

Make verify and then go to this channel and paste your wallet address at [#id-5.3.-list-all-keys](airchains.md#id-5.3.-list-all-keys "mention") to get your faucet [https://discord.com/channels/1116269224449548359/1238910689188511835](https://discord.com/channels/1116269224449548359/1238910689188511835)

### 7.5. Delegate token to your own validator

{% code overflow="wrap" %}
```bash
junctiond tx staking delegate $(junctiond keys show wallet --bech val -a)  1000000amf 
--from wallet --chain-id junction --fees 5000amf -y
```
{% endcode %}

With "**wallet**" is your wallet name create or recovery at [#id-5.1.-add-new-wallet-key](airchains.md#id-5.1.-add-new-wallet-key "mention")or [#id-5.2.-or-recover-existing-key](airchains.md#id-5.2.-or-recover-existing-key "mention").

## 8. Withdraw rewards and commission from your validator

{% code overflow="wrap" %}
```bash
junctiond tx distribution withdraw-rewards $(junctiond keys show wallet --bech val -a) --from wallet --chain-id junction --fees 5000amf -y
```
{% endcode %}

## 9. Unjail validator

{% code overflow="wrap" %}
```bash
junctiond tx slashing unjail --from wallet --chain-id junction --fees 5000amf -y
```
{% endcode %}

## 10. Services Management

#### **10.1. Reload service**

```bash
sudo systemctl daemon-reload
```

#### **10.2. Enable service**

<pre class="language-bash"><code class="lang-bash"><strong>sudo systemctl enable junctiond
</strong></code></pre>

#### **10.3. Disable service**

```bash
sudo systemctl disable junctiond
```

#### 10.4. Start service

```bash
sudo service junctiond start
```

#### 10.5. Stop service

```bash
sudo service junctiond stop 
```

#### 10.6. Restart service

```bash
sudo service junctiond restart 
```

#### 10.7. Check service status

```bash
sudo systemctl junctiond status 
```

#### 10.8. Check service logs

```
sudo journalctl -u junctiond -f --no-hostname -o cat
```

## 3. Backup Validator - <mark style="color:red;">**Important**</mark>

```bash
cat $HOME/.junction/config/priv_validator_key.json
```

<mark style="color:red;">**Copy and store your node information in safe location.**</mark>

## 4. Remove node

Make sure you want to remove node, keep seed pharse at [#id-5.-create-or-import-wallet](airchains.md#id-5.-create-or-import-wallet "mention") and validator information at [#id-3.-backup-validator-important](airchains.md#id-3.-backup-validator-important "mention"). <mark style="color:red;">**Once deleted, it cannot be restored**</mark>

```bash
sudo service junctiond stop 
sudo systemctl disable junctiond
rm -rf /etc/systemd/system/junctiond.service
sudo systemctl daemon-reload
sudo rm -f $(which junctiond)
sudo rm -rf $HOME/.junction
```

## 5. Snapshot

```bash
sudo service junctiond stop
mv $HOME/.junction/data/priv_validator_state.json $HOME/.junction/priv_validator_state.json.backup
rm -rf $HOME/.junction/data $HOME/.junction/wasmPath

wget -O- https://snap.anonid.top/snaps%2Fairchains.snap.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.junction

mv $HOME/.junction/priv_validator_state.json.backup $HOME/.junction/data/priv_validator_state.json

sudo service junctiond restart && sudo journalctl -u junctiond -f --no-hostname -o cat
```

## 6. State Sync

```bash
sudo service junctiond stop 

cp $HOME/.junction/data/priv_validator_state.json $HOME/.junction/priv_validator_state.json.backup
junctiond tendermint unsafe-reset-all --home $HOME/.junction

peers="de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656,1918bd71bc764c71456d10483f754884223959a5@35.240.206.208:26656,48887cbb310bb854d7f9da8d5687cbfca02b9968@35.200.245.190:26656,de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656,8b72b2f2e027f8a736e36b2350f6897a5e9bfeaa@131.153.232.69:26656,d92c7efcb453ba2edab6d80ad6e3b692e3a7e4f5@49.13.120.225:26656,5c5989b5dee8cff0b379c4f7273eac3091c3137b@57.128.74.22:56256,e09fa8cc6b06b99d07560b6c33443023e6a3b9c6@65.21.131.187:26656,0305205b9c2c76557381ed71ac23244558a51099@162.55.65.162:26656,3e5f3247d41d2c3ceeef0987f836e9b29068a3e9@168.119.31.198:56256,086d19f4d7542666c8b0cac703f78d4a8d4ec528@135.148.232.105:26656,976a0fe0a0fa205478beb66addaae3842907c3f6@37.27.48.77:32656,7d6694fb464a9c9761992f695e6ba1d334403986@164.90.228.66:26656,b2e9bebc16bc35e16573269beba67ffea5932e13@95.111.239.250:26656,23152e91e3bd642bef6508c8d6bd1dbedccf9e56@95.111.237.24:26656,c1e9d12d80ec74b8ddbabdec9e0dad71337ba43f@135.181.82.176:26656,3b429f2c994fa76f9443e517fd8b72dcf60e6590@37.27.11.132:26656,84b6ccf69680c9459b3b78ca4ba80313fa9b315a@159.69.208.30:26656,e78a440c57576f3743e6aa9db00438462980927e@5.161.199.115:26656,49fb1316b22c71e455720af15dd552dafb9af39a@5.189.151.175:26656,e831fa909cce0d1807cfcf417e28e782530f5c94@161.97.66.254:26666,db38d672f66df4de01b26e1fa97e1632fbfb1bdf@173.249.57.190:26656,08a0014125bded5fe76b9dc3275b0a58b6841b43@116.203.184.36:26656,6025c1523ad0cd6926ef277cfcf46d82ebb43c21@116.203.24.46:26656,fed2e80e159a23bf9f71f980b501c2304cec2f6d@185.194.216.61:17656,1ad9bdeac0b06f585a9c64261d0705c4cbfd28e7@144.91.99.93:26656,a6384bd23bd728ffa9a8452b12fc865dadf51672@81.200.154.160:26656,5b31fdf605645b44ad615c8b79b1550540895fe5@35.214.147.230:26656,6a3a13d7631823eb6dcd00882243c913c819a125@38.242.196.100:26656,3e182e463425dfa6d4cef83f4bdd67c98c36eba3@195.26.243.208:47656,c97c7a9c11cf3cb059ce89c36f7ff219daa3ada4@195.26.246.26:26656,7d162ef2392d25720d7cdb2cfdf2ccf146e32bac@49.13.234.149:26656,5a161464aa73571f1b7e22204bcf3bdb6fb71f3b@195.26.241.184:26656,bff7c802021ed3b4aaf222da9d42280bfc5dad88@65.109.139.181:26656,c284cbda3ab197001136c39c9df8e45af2038513@34.93.143.222:26656,449297568d9d6d4aa51a93f7a1b1e92e1ec38619@65.108.242.9:26656,611440c7193678ec1cd0c60b55abfca07dfa27cd@95.217.161.97:26656,beec52199d4b28cab6fc3b2f2a2718c6667ac46a@37.27.19.95:26656,226b9c42e81cddd185d435348cd89f87fee37279@135.181.42.46:26656,479b63df84e247c55e80cccd9abbf7100a334fcc@65.108.156.83:26656"

SNAP_RPC="https://junction-testnet-rpc.nodesync.top:443"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.junction/config/config.toml 

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height);
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000));
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) 

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH && sleep 2

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ;
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ;
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ;
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ;
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.junction/config/config.toml

mv $HOME/.junction/priv_validator_state.json.backup $HOME/.junction/data/priv_validator_state.json

sudo service junctiond restart && sudo journalctl -u junctiond -f --no-hostname -o cat
```
