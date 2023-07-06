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
cd && mkdir execution && wget https://github.com/NethermindEth/nethermind/releases/download/1.19.3/nethermind-1.19.3-e8ac1da4-linux-x64.zip
unzip nethermind-1.19.3-e8ac1da4-linux-x64.zip -d execution

```
## 4. Lighthouse indirelim.
```
cd && mkdir consensus && wget https://github.com/sigp/lighthouse/releases/download/v4.2.0/lighthouse-v4.2.0-x86_64-unknown-linux-gnu-portable.tar.gz
tar -xvf lighthouse-v4.2.0-x86_64-unknown-linux-gnu-portable.tar.gz --directory  consensus

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
ExecStart=/root/execution/Nethermind.Runner --config chiado  --Sync.FastSync true --Pruning.Mode=Full --JsonRpc.Enabled true --Init.WebSocketsEnabled true --JsonRpc.Host 0.0.0.0 --JsonRpc.WebSocketsPort 8545 --HealthChecks.Enabled true --HealthChecks.UIEnabled true --JsonRpc.EnginePort=8551 --JsonRpc.JwtSecretFile=~/jwtsecret/jwt.hex --Discovery.Bootnodes enode://7dd44af6138120f328bb031eb56e00985c149319d4f1e33275b30be7fddadd8ccd9f7b9c3b35a16136a61e85b2b2d1de073f30ec1d0ddf576a33be8ff48d88d0@139.144.26.89:30303,enode://317b9cee65ccf1d747b00e604242bfa3ae367beee8f149e28c5b2b88820f855ea7b5a75eb5327cfc3d8ca97adbf71538468290a46592ed7009f3fb394ec752f1@139.144.26.115:30303,enode://b77ae97906155ebbb83fd32c87ab0aa57372a24abbd8aa4bae679f048b726de4a195709f613be4981e44b24640bc89e4824427d94e9a37afc148da8250c8ab2d@139.144.26.101:30303,enode://69f8abfa3b0221161f8c19014b90857a18742554af27af73fd779c486728750a0ff11b873975f104fc5276a3a7c3b5b68cb3c26c815e9f78462901895d652124@139.144.26.85:30303,enode://ac7fc76f9b2ab343fb2d091365a7f46d17018e525cbedfbf24b247c76657e934ef4df61cc2f6dad6bfcf722425e03e1a8a6e4e4b52743acc2319cb8ebf27d742@170.187.154.239:30303,enode://712144ac396fd2298b3e2559e2930d7f3a36fded3addd66955224958f1845634067717ab9522757ed2948f480fc52add5676487c8378e9011a7e2c0ac2f36cc3@3.71.132.231:30303,enode://595160631241ea41b187b85716f9f9572a266daa940d74edbe3b83477264ce284d69208e61cf50e91641b1b4f9a03fa8e60eb73d435a84cf4616b1c969bc2512@3.69.35.13:30303
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
Description=Lighthouse: Gnosis Beacon Node
After=syslog.target network.target

[Service]
User=root
Type=simple
ExecStart=/root/consensus/lighthouse --testnet-dir=/root/consensus/network_config bn --http --execution-endpoint http://localhost:8551 --execution-jwt ~/jwtsecret/jwt.hex --checkpoint-sync-url "https://checkpoint.gnosischain.com" --disable-deposit-contract-sync --boot-nodes=enr:-L64QOijsdi9aVIawMb5h5PWueaPM9Ai6P17GNPFlHzz7MGJQ8tFMdYrEx8WQitNKLG924g2Q9cCdzg54M0UtKa3QIKCMxaHYXR0bmV0c4j__________4RldGgykDE2cEMCAABv__________-CaWSCdjSCaXCEi5AaWYlzZWNwMjU2azGhA8CjTkD4m1s8FbKCN18LgqlYcE65jrT148vFtwd9U62SiHN5bmNuZXRzD4N0Y3CCIyiDdWRwgiMo
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
## 7. Nethermind ve Lighthouse başlatalım.
```
sudo systemctl enable nethermind
sudo systemctl enable lighthouse
sudo systemctl daemon-reload
sudo systemctl start nethermind
sudo systemctl start lighthouse

```
## 8. Log kontrol
```
sudo journalctl -fo cat -u nethermind
sudo journalctl -fo cat -u lighthouse
```
## 9. RPC 
```
http://IP:8545
ws://IP:8546
```
