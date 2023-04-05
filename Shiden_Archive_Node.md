## Sunucu özellikleri şunlar:
#### 16GB RAM
#### 8 vCPUs
#### At least 450 GB of storage
#### Ubuntu 20.04

## 1. Güncelle
```
sudo apt update && sudo apt upgrade -y

```

## 2. Gereklileri yükle
```
sudo apt-get install git curl wget screen jq net-tools lz4 zip unzip mlocate unattended-upgrades net-tools whois apt-transport-https ca-certificates libssl-dev gnupg bash-completion needrestart node-ws -y

```
## 3. Kullanıcı ve klasörleri oluştur
```
sudo adduser shiden --system --no-create-home
mkdir -p /var/lib/shiden-data && cd /var/lib/shiden-data

```
## 4. Dosyaları indirerek ayarlamaları yapalım.
```
wget https://github.com/AstarNetwork/Astar/releases/download/v4.29.1/astar-collator-v4.29.1-ubuntu-x86_64.tar.gz
tar -xvf astar-collator-v4.29.1-ubuntu-x86_64.tar.gz
sudo chown -R shiden /var/lib/shiden-data
sudo chmod ugo+x /var/lib/shiden-data/astar-collator

```
## 5. Systemd dosyası oluşturalım
#### Burada yazan thereisnopoon'u düzeltmeyi unutmayın!

```
sudo tee <<EOF >/dev/null /etc/systemd/system/shiden.service
[Unit]
Description="Shiden Node"

[Service]
Type=simple
Restart=on-failure
RestartSec=10
User=shiden
SyslogIdentifier=shiden
SyslogFacility=local7
KillSignal=SIGHUP
ExecStart=/var/lib/shiden-data/astar-collator \
     --port 30333 \
     --rpc-port 9933 \
     --ws-port 9944 \
     --execution wasm \
     --pruning archive \
     --rpc-cors all \
     --base-path /var/lib/shiden-data \
     --chain shiden \
     --name thereisnospoon \
     --enable-evm-rpc \
     --trie-cache-size 1 \
     --rpc-max-response-size 128 \
     --ws-max-connections 10000 \
     --unsafe-rpc-external \
     --unsafe-ws-external

[Install]
WantedBy=multi-user.target
EOF
```
## 6. Shiden'i başlatalım.
```
sudo systemctl daemon-reload
sudo systemctl enable shiden.service
sudo systemctl start shiden.service


```
## 7. Log Kontrol
```
journalctl -f -u shiden.service -n100

```
## 8. update
```
wget https://github.com/AstarNetwork/Astar/releases/download/v5.3.0/astar-collator-v5.3.0-ubuntu-x86_64.tar.gz
tar -xvf astar-collator-v5.3.0-ubuntu-x86_64.tar.gz

```
```
sudo mv ./astar-collator /var/lib/shiden-data
sudo chmod +x /var/lib/shiden-data/astar-collator
sudo systemctl restart shiden.service

```



