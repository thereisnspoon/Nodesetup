
## Sunucu özellikleri şunlar:
#### 16GB RAM
#### 4 vCPUs
#### At least 1TB storage

## Kurulumda execution olarak geth ve consensus olarak lighthouse kullanılacaktır.

## 1. Geth kuralım ve güncelleyelim.
```
sudo apt-get install software-properties-common screen ufw -y
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get install ethereum -y
sudo apt update && sudo apt upgrade -y

```
## 2. Port ayarlarını yapalım.
```
sudo ufw allow 30303

```
## 3. Secret file ve config file oluşturalım.
```
mkdir jwtsecret && cd jwtsecret && openssl rand -hex 32 | tr -d "\n" > "/root/jwtsecret/jwt.hex"

```
```
sudo tee <<EOF >/dev/null /root/jwtsecret/config.toml
[Eth]

SyncMode = "snap"
NetworkId=1
EthDiscoveryURLs = ["enrtree://AKA3AM6LPBYEUDMVNU3BSVQJ5AD45Y7YPOHJLEF6W26QOE4VTUDPE@all.mainnet.ethdisco.net"]
SnapDiscoveryURLs = ["enrtree://AKA3AM6LPBYEUDMVNU3BSVQJ5AD45Y7YPOHJLEF6W26QOE4VTUDPE@all.mainnet.ethdisco.net"]

NoPruning = false
NoPrefetch = false
TxLookupLimit = 2350000
LightPeers = 100
UltraLightFraction = 75
DatabaseCache = 2048
DatabaseFreezer = ""
TrieCleanCache = 614
TrieCleanCacheJournal = "triecache"
TrieCleanCacheRejournal = 3600000000000
TrieDirtyCache = 1024
TrieTimeout = 3600000000000
SnapshotCache = 409
Preimages = false
EnablePreimageRecording = false
RPCGasCap = 50000000
RPCTxFeeCap = 1e+00
[Eth.Miner]
GasFloor = 0
GasCeil = 8000000
GasPrice = 1000000000
Recommit = 3000000000
Noverify = false

[Eth.Ethash]
CacheDir = "ethash"
CachesInMem = 2
CachesOnDisk = 3
CachesLockMmap = false
DatasetDir = "/root/.ethash"
DatasetsInMem = 1
DatasetsOnDisk = 2
DatasetsLockMmap = false
PowMode = 0
NotifyFull = false

[Eth.TxPool]
Locals = []
NoLocals = false
Journal = "transactions.rlp"
Rejournal = 3600000000000
PriceLimit = 1
PriceBump = 10
AccountSlots = 16
GlobalSlots = 5120
AccountQueue = 64
GlobalQueue = 1024
Lifetime = 10800000000000

[Eth.GPO]
Blocks = 20
Percentile = 60
MaxHeaderHistory = 1024
MaxBlockHistory = 1024
MaxPrice = 500000000000
IgnorePrice = 2

[Node]
IPCPath = "geth.ipc"
HTTPHost = "0.0.0.0"
InsecureUnlockAllowed = false
HTTPPort = 8545
HTTPVirtualHosts = ["*"]
HTTPModules = ["eth", "net", "web3", "txpool", "personal","debug"]
WSPort = 8546
WSModules = ["eth", "net", "web3","txpool", "personal","debug"]
WSHost = "0.0.0.0"
DataDir = "/node/data"
HTTPCors = ["*"]
WSOrigins = ["*"]

[Node.P2P]
MaxPeers = 60
NoDiscovery = false

StaticNodes = []
TrustedNodes = []
ListenAddr = ":30303"
EnableMsgEvents = false

[Node.HTTPTimeouts]
ReadTimeout = 30000000000
WriteTimeout = 30000000000
IdleTimeout = 120000000000

[Metrics]
Enabled = true
HTTP = "0.0.0.0"
Port = 6060
EOF
```
## 4. Geth çalıştıralım.
```
cd
screen -S ethereum

```
```
geth --config=/root/jwtsecret/config.toml --authrpc.jwtsecret /root/jwtsecret/jwt.hex --authrpc.port 8551 --bootnodes enode://6f8a80d14311c39f35f516fa664deaaaa13e85b2f7493f37f6144d86991ec012937307647bd3b9a82abe2974e1407241d54947bbb39763a4cac9f77166ad92a0@10.3.58.6:30303?discport=30301

```
## 5. Lighthouse kurarak çalıştıralım.
### Screenden çıkmadan CTRL + A + C ile yeni session açalım.

```
cd && mkdir consensus && wget https://github.com/sigp/lighthouse/releases/download/v3.3.0/lighthouse-v3.3.0-x86_64-unknown-linux-gnu-portable.tar.gz
tar -xvf lighthouse-v3.3.0-x86_64-unknown-linux-gnu-portable.tar.gz --directory  consensus
cd consensus

```
```
./lighthouse bn \
  --network mainnet \
  --execution-endpoint http://localhost:8551 \
  --execution-jwt /root/jwtsecret/jwt.hex \
  --checkpoint-sync-url https://mainnet.checkpoint.sigp.io \
  --disable-deposit-contract-sync
  
```
## 6. Sync kontrol
```
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "eth_blockNumber","params": []}' localhost:8545

```
## 7. RPC 
```
http://IP:8545
ws://IP:8546
```

