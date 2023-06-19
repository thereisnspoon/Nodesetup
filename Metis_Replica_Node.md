## Sunucu özellikleri şunlar:
#### 8GB RAM
#### 4 vCPUs
#### At least 150GB storage
#### Orjinal kaynak: https://github.com/ericlee42/metis-replica-node

## 1. Güncelle
```
sudo apt update && sudo apt upgrade -y
```

## 2. Gereklileri yükle
```
sudo apt install apt-transport-https ca-certificates curl jq software-properties-common -y
```
## 3. Docker
```
sudo apt-get install ca-certificates curl gnupg lsb-release -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io -y
```

## 4. Docker compose
```
docker_compose_version=$(wget -qO- https://api.github.com/repos/docker/compose/releases/latest | jq -r ".tag_name")
sudo wget -O /usr/bin/docker-compose "https://github.com/docker/compose/releases/download/${docker_compose_version}/docker-compose-`uname -s`-`uname -m`"
sudo chmod +x /usr/bin/docker-compose
```

## 5. Repoyu Clonla ve düzenlemeleri yap.
```
git clone https://github.com/ericlee42/metis-replica-node
```
```
cd metis-replica-node
```
```
cp docker-compose-mainnet.yml docker-compose.yml
```

Aşadağıdaki kod ile dosyayı düzeltirken `DATA_TRANSPORT_LAYER__L1_RPC_ENDPOINT` alanının devamına blastapi,infura, alchmeyden alacağınız Ethereum mainnet apilerini ekleyin.

```
sudo nano docker-compose.yml
```
CTRL+O // ENTER // CTRL+X ile kapatın.

## 6. DTL ve Layer2GETH başlatalım.
```
docker-compose up -d dtl
```
```
docker-compose up -d l2geth
```
## 7. Loglara bakış.
```
docker-compose logs --tail=10 dtl
docker-compose logs --tail=10 l2geth
```
## 8. IP ve Portlar.
```
http://IP:8545
ws://IP:8546
```

