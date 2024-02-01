## Sunucu özellikleri şunlar:
#### 16GB RAM
#### 4 vCPUs
#### 
#### Ubuntu 20.04

## 1. Güncelle
```
sudo apt update && sudo apt upgrade -y

```

## 2.a. Gereklileri yükle
```
sudo apt-get install libsnappy-dev libc6-dev libc6 unzip screen -y

```
## 2.b. Gereklileri yükle
```
mkdir jwtsecret && openssl rand -hex 32 | tr -d "\n" > "./jwtsecret/jwt.hex"

```
## 3. Nethermind indirelim.
```
cd && mkdir execution && wget https://github.com/NethermindEth/nethermind/releases/download/1.25.3/nethermind-1.25.3-e64bee70-linux-x64.zip
unzip nethermind-1.25.3-e64bee70-linux-x64.zip -d execution

```
## 4. Lighthouse indirelim.
```
cd && mkdir consensus && wget https://github.com/sigp/lighthouse/releases/download/v4.6.0/lighthouse-v4.6.0-x86_64-unknown-linux-gnu-portable.tar.gz
tar -xvf lighthouse-v4.6.0-x86_64-unknown-linux-gnu-portable.tar.gz --directory  consensus

```
## 5. Nethermind systemd oluşturalım. 
```
sudo tee <<EOF >/dev/null /etc/systemd/system/nethermind.service
[Unit]
Description=Nethermind
After=network.target

[Service]
User=root
Type=simple
ExecStart=/root/execution/Nethermind.Runner --config chiado  --Sync.FastSync true --Pruning.Mode=Full --JsonRpc.Enabled true --Init.WebSocketsEnabled true --JsonRpc.Host 0.0.0.0 --JsonRpc.WebSocketsPort 8545 --HealthChecks.Enabled true --HealthChecks.UIEnabled true --JsonRpc.EnginePort=8551 --JsonRpc.JwtSecretFile=/root/jwtsecret/jwt.hex --Discovery.Bootnodes enode://712144ac396fd2298b3e2559e2930d7f3a36fded3addd66955224958f1845634067717ab9522757ed2948f480fc52add5676487c8378e9011a7e2c0ac2f36cc3@3.71.132.231:30303
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

```
## 6. Lighthouse systemd oluşturalım.
```
sudo tee <<EOF >/dev/null /etc/systemd/system/lighthouse.service
[Unit]
Description=Lighthouse: Chiado Beacon Node
After=syslog.target network.target

[Service]
User=root
Type=simple
ExecStart=/root/consensus/lighthouse \
--network chiado bn \
--http \
--execution-endpoint http://localhost:8551 \
--execution-jwt /root/jwtsecret/jwt.hex \
--disable-deposit-contract-sync \
--boot-nodes=enr:-L64QKYKGQj5ybkfBxyFU5IEVzP7oJkGHJlie4W8BCGAYEi4P0mmMksaasiYF789mVW_AxYVNVFUjg9CyzmdvpyWQ1KCMlmHYXR0bmV0c4j__________4RldGgykDE2cEMCAABv__________-CaWSCdjSCaXCEi5CtNolzZWNwMjU2azGhAuA7BAwIijy1z81AO9nz_MOukA1ER68rGA67PYQ5pF1qiHN5bmNuZXRzD4N0Y3CCIyiDdWRwgiMo \
--checkpoint-sync-url https://checkpoint.chiadochain.net
KillMode=process
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
## 7.
```
sudo systemctl enable nethermind
sudo systemctl enable lighthouse
sudo systemctl daemon-reload

```
## 8.
```
sudo systemctl start nethermind && journalctl -fo cat -u nethermind
```
Bir süre çalışmasını bekleyip consensustan mesaj beklerse ctrl+c ile durdurup aşağıdaki kodu girin.

```
sudo systemctl start lighthouse && sudo journalctl -fo cat -u lighthouse
```
## 9. Log kontrol
```
sudo journalctl -fo cat -u nethermind
sudo journalctl -fo cat -u lighthouse
```


## 10. RPC 
```
http://IP:8545
ws://IP:8545
```
```
sudo systemctl stop nethermind
```
```
sudo systemctl stop lighthouse
```
```
sudo systemctl restart nethermind && journalctl -fo cat -u nethermind
```
```
sudo systemctl restart lighthouse  && sudo journalctl -fo cat -u lighthouse
```
