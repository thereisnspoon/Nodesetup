## Sunucu özellikleri şunlar:
#### 16GB RAM
#### 4 vCPUs
#### At least 2 TB of storage
### kaynak: https://docs.fantom.foundation/node/run-a-read-only-node
## 1. Güncelle
```
sudo apt update && sudo apt upgrade -y

```
## 2. Gereklileri yükle
```
sudo apt-get install build-essential git screen tar unzip  -y

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
## 6. gpt ve snapshot indirelim
sıradaki işlemler uzun süreceği için screenden CTRL + A + D ile çıkabilirsiniz.
```
cd build
wget https://download.fantom.network/mainnet-109331-pruned-mpt.g
```
bu kısım tamamlanınca...

```
cd && mkdir download && cd download
wget https://snapshot1.fantom.network/files/pruned/snapshot-15-Dec-2022-10-24/
```
bu guide yazıldığı sırada güncel snapshot 15 December idi, daha günceline https://snapshot1.fantom.network/files/pruned/ adresinden ulaşabilirsiniz.
İndirme işlemleri uzun sürecek, screenden çıkarak ara ara kontrol edin, tamamlanınca 7. kısımdan devam edin.
## 7. dosyaları açalım ve taşıyalım
```

```
