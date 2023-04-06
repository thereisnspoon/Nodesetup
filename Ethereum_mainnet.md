
## Sunucu özellikleri şunlar:
#### 16GB RAM
#### 4 vCPUs
#### At least 1TB storage

## Kurulumda execution olarak geth ve consensus olarak lighthouse kullanılacaktır.

## 1. Geth kuralım ve güncelleyelim.
```
sudo apt-get install software-properties-common screen ufw -y
```
```
sudo add-apt-repository -y ppa:ethereum/ethereum
```
```
sudo apt-get install ethereum -y
```
```
sudo apt update && sudo apt upgrade -y
```
## 2. Port ayarlarını yapalım.
```
sudo ufw allow 30303

```
## 3. Secret file oluşturalım.
```
mkdir jwtsecret && openssl rand -hex 32 | tr -d "\n" > "./jwtsecret/jwt.hex"

```
## 4. Geth systemd oluşturalım.
```
sudo tee <<EOF >/dev/null /etc/systemd/system/geth.service
[Unit]
Description=Geth Execution Layer Client service
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=$USER
Restart=on-failure
RestartSec=3
TimeoutSec=300
ExecStart=/usr/bin/geth \
  --mainnet \
  --syncmode snap \
  --datadir "/data/ethereum" \
  --http \
  --authrpc.addr localhost \
  --authrpc.vhosts="localhost" \
  --authrpc.port 8551 \
  --authrpc.jwtsecret=/root/jwtsecret/jwt.hex \
  --http.api net,eth,personal,web3,engine,admin \
  --http.addr 0.0.0.0 \
  --http.port 8545 \
  --ws \
  --ws.addr 0.0.0.0 \
  --ws.port 8546 \
  --rpc.gascap 150000000 \
  --ws.api net,eth,personal,web3

[Install]
WantedBy=multi-user.target
EOF

```

## 5. Lighthouse indirelim

```
cd && mkdir consensus && wget https://github.com/sigp/lighthouse/releases/download/v4.0.1/lighthouse-v4.0.1-x86_64-unknown-linux-gnu-portable.tar.gz
tar -xvf lighthouse-v4.0.1-x86_64-unknown-linux-gnu-portable.tar.gz --directory  consensus

```
## 6. Lighthouse systemd oluşturalım

```
sudo tee <<EOF >/dev/null /etc/systemd/system/lighthouse.service
[Unit]
Description=Lighthouse: Ethereum Beacon Node
After=syslog.target network.target

[Service]
User=root
Type=simple
ExecStart=/root/consensus/lighthouse \
--network mainnet beacon_node \
--execution-endpoint http://localhost:8551 \
--execution-jwt /root/jwtsecret/jwt.hex \
--checkpoint-sync-url https://mainnet.checkpoint.sigp.io \
--disable-deposit-contract-sync
KillSignal=SIGINT
TimeoutStopSec=90
Restart=on-failure
RestartSec=10s
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=lighthouse-bn

[Install]
WantedBy=multi-user.target
EOF

```
## 7. Her iki layer'ı da başlatalım
```
sudo systemctl enable geth
sudo systemctl enable lighthouse
sudo systemctl daemon-reload
sudo systemctl start geth
sudo systemctl start lighthouse

```
## 8. Log kontrol
```
sudo journalctl -u geth -f
sudo journalctl -u lighthouse -f
```

## 9. Sync kontrol
```
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "eth_blockNumber","params": []}' localhost:8545

```
## 10. RPC 
```
http://IP:8545
ws://IP:8546
```

## 11. Capella/Shanghai ("Shapella") upgrade
```
sudo systemctl stop geth && sudo systemctl stop lighthouse
```
```
sudo apt update && sudo apt upgrade -y
```
```
geth version-check
``` 
#### ÜSTTEKİ KODDAN SONRA "version=Geth/v1.11.5-stable" çıktısı almalısınız.


```
wget https://github.com/sigp/lighthouse/releases/download/v4.0.1/lighthouse-v4.0.1-x86_64-unknown-linux-gnu-portable.tar.gz
```
```
tar -xvf lighthouse-v4.0.1-x86_64-unknown-linux-gnu-portable.tar.gz
```
```
sudo mv ./lighthouse /root/consensus
```
```
sudo chmod +x /root/consensus/lighthouse
```
```
sudo systemctl daemon-reload
```
```
sudo systemctl start geth
```
#30SN KADAR BEKLEDİKTEN SONRA ALTTAKİ KODA GEÇİN

```
sudo systemctl start lighthouse
```
```
sudo journalctl -u lighthouse -f
```
#### EN SONKİ KODLA baktığınızda loglar arasında "Ready for Capella" görmelisiniz. GÖRÜYORSANIZ GÜNCELLEME BAŞARILI OLMUŞTUR.
