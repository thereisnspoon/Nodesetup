## Sunucu özellikleri şunlar:
#### 16GB RAM
#### 4 vCPUs
#### En az 1.8 TB of storage
### Kaynak: 
#### https://docs.fantom.foundation/node/run-a-read-only-node 
#### https://docs.blastapi.io/running-a-node/supported-chains/fantom/mainnet
## 1. Güncelle
```
sudo apt update && sudo apt upgrade -y

```
## 2. Gereklileri yükle
```
sudo apt-get install build-essential git screen tar unzip node-ws  -y

```
## 3. Go yükle
```
ver="1.19.1"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```
## 4. Screen açalım
```
screen -S fantom
```
## 5. Fantom kuralım
```
git clone https://github.com/Fantom-foundation/go-opera.git
cd go-opera/
git checkout release/1.1.1-rc.2
make
```
## 6. Genesis file ve Snapshot indirelim
sıradaki işlemler uzun süreceği için screenden CTRL + A + D ile çıkabilirsiniz.
```
cd build
wget https://download.fantom.network/mainnet-109331-pruned-mpt.g
```
bu kısım tamamlanınca...

```
cd && mkdir download && cd download
wget https://snapshot1.fantom.network/files/pruned/snapshot-15-Dec-2022-10-24/opera-pruned_15-Dec-2022-10-24.tar.gz
```
bu guide yazıldığı sırada güncel snapshot 15 December idi, daha günceline https://snapshot1.fantom.network/files/pruned/ adresinden ulaşabilirsiniz.
İndirme işlemleri uzun sürecek, screenden çıkarak ara ara kontrol edin, tamamlanınca 7. kısımdan devam edin.
## 7. Dosyaları açalım, taşıyalım ve yer kazanalım.
```
tar zxvf opera-pruned_15-Dec-2022-10-24.tar.gz
```
```
mv ~/download/.opera ~/.opera
```
```
rm -rf ~/download
```
## 8. Systemd dosyası oluşturalım.
```
Node_ismi=<bir_Node_ismi_gir> #bu kodu "<>" dahil düzenleyerek girin.
```
```
sudo tee <<EOF >/dev/null /etc/systemd/system/fantom.service
[Unit]
Description=Fantom Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/root/go-opera/build/opera --genesis /root/go-opera/build/mainnet-109331-pruned-mpt.g --identity $Node_ismi --cache 8096 --http --http.addr 0.0.0.0 --http.vhosts=* --http.corsdomain=* --ws --ws.addr 0.0.0.0 --ws.origins=* --datadir.minfreedisk=8096 --http.api="ftm,eth,abft,dag,rpc,web3,net,debug" --ws.api="ftm,eth,abft,dag,rpc,web3,net,debug"
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## 9. Node'u başlatalım.
```
sudo systemctl enable fantom
sudo systemctl daemon-reload
sudo systemctl start fantom
```
## 10. Log kontrol
```
sudo journalctl -fo cat -u fantom
```
## 11. Sync kontrol
Node'unuz senkronize olduysa şu çıktıyı verecektir. {"jsonrpc":"2.0","id":1,"result":false}
```
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "eth_syncing", "params":[]}' localhost:18545
```
## 12. RPC
```
http://IP:18545
ws://IP:18546
```
```
wscat --connect ws://localhost:18546
{"id":1, "jsonrpc":"2.0", "method": "eth_blockNumber","params": []}
```
## 13. Güncel Blok için Explorer
https://ftmscan.com



