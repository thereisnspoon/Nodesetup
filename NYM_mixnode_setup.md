## 1. Güncelleştirme yapalım
```
sudo apt update && sudo apt upgrade -y
```
## 2. Değişkenleri atayalım 
### !!Burada ilgili yerleri değiştirerek komutlayın.
```
node_id=nodeismiyazin
```
```
wallet_address=walletadresyazin
```
## 3. Gereklilikleri yükleyelim.
```
sudo apt install pkg-config build-essential libssl-dev curl jq ufw snapd base58 -y
```
## 4. Mixnode 1.1.9'u yükleyelim
```
wget -O nym-mixnode https://github.com/nymtech/nym/releases/download/nym-binaries-v1.1.9/nym-mixnode
```
```
chmod +x nym-mixnode
```
```
sudo mv nym-mixnode /usr/bin/
```
## 5. Mixnode'a değişkenleri tanımlayalım
### !!Burada identity ve sphinx keylerimizi not alalım.
```
nym-mixnode init --id $node_id --host $(curl ifconfig.me) --wallet-address $wallet_address
```


## 6. Portları açalım.
```
sudo ufw allow 1789,1790,8000,22,80,443/tcp
```
## 7. Service dosyası oluşturalım.
```
sudo tee <<EOF >/dev/null /etc/systemd/system/nym-mixnode.service
[Unit]
Description=Nym Mixnode (v1.1.9)
StartLimitInterval=350
StartLimitBurst=10

[Service]
User=$USER
LimitNOFILE=65536
ExecStart=/usr/bin/nym-mixnode run --id '$node_id'
KillSignal=SIGINT
Restart=on-failure
RestartSec=30

[Install]
WantedBy=multi-user.target
EOF
```
## 7. Mixnode'u çalıştıralım.
```
sudo systemctl enable nym-mixnode
sudo systemctl daemon-reload
sudo systemctl start nym-mixnode

```
## 7. Loglar ile kontrol edelim.
```
sudo journalctl -u nym-mixnode -f
```

