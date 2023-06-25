## Sunucu özellikleri şunlar:
#### 8GB RAM
#### 4 vCPUs
#### At least 200GB storage
## 1. Gereklilikleri yükleyelim ve güncelleyelim.
```
sudo apt install software-properties-common libjemalloc-dev default-jdk default-jre screen curl ufw -y
```
```
sudo apt update && sudo apt upgrade -y
```
## 2. Dizine config, genesis file ve besu kuralım.
```
mkdir palm-node && cd palm-node
```
```
wget https://hyperledger.jfrog.io/hyperledger/besu-binaries/besu/23.4.1/besu-23.4.1.tar.gz
tar -xvf besu-23.4.1.tar.gz
```
```
curl -O https://genesis-files.palm.io/prd/genesis.json
```
```
sudo tee <<EOF >/dev/null /root/palm-node/config.toml
# Palm Mainnet genesis file
genesis-file="genesis.json"

# Network bootnodes
bootnodes=["enode://9cccbaae702d477c5fd4d704a2d6f92a90005f62de980b11b0d042877bf759774cf7d68d358c59427622e87538bc46afa1195d6ac12cb153d6771461c1830d1b@54.243.108.56:30303","enode://d6518f4f318a172158cf73c3e615c4eb488efb14c20b4a2f13570bf01092573222cd6935599a80017512457fb7f229cf6562f9d038b5d0dc98db95074d4a98b3@18.235.247.31:30303"]

# Data directory
data-path="/root/palm-node"

# Enable the JSON-RPCs
rpc-http-enabled=true

# Specify required API methods
rpc-http-api=["ETH","NET","WEB3","ADMIN","IBFT","TXPOOL","DEBUG","TRACE"]
EOF
```
## 3. Besu için Systemd oluşturalım.
```
sudo tee <<EOF >/dev/null /etc/systemd/system/besu.service
[Unit]
Description=Lighthouse: Palm Node
After=syslog.target network.target

[Service]
User=root
Type=simple
ExecStart=/root/palm-node/besu \
--config-file=/path/to/config.toml \
--sync-mode=FAST \
--random-peer-priority-enabled=true \
--rpc-http-enabled=true \
--rpc-http-api=ETH,NET,WEB3,ADMIN,IBFT,TXPOOL,DEBUG,TRACE \
--rpc-ws-api=ETH,NET,WEB3,ADMIN,IBFT,TXPOOL,DEBUG,TRACE \
--rpc-ws-enabled \
--rpc-http-host=0.0.0.0 \
--rpc-ws-host=0.0.0.0 \
--host-allowlist=* \
--metrics-enabled \
--metrics-host=0.0.0.0 \
--rpc-http-cors-origins=* \
--rpc-http-max-active-connections=10000 \
--rpc-ws-max-active-connections=10000 \
--max-peers=100

[Install]
WantedBy=multi-user.target
EOF
```
## 4. Başlatalım.
```
sudo systemctl daemon-reload
sudo systemctl start besu
```
## 5.Log kontrol
```
sudo journalctl -u besu -f
```
## 6. RPC 
```
http://IP:8545
ws://IP:8546
```
