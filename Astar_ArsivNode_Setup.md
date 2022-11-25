## 1. Güncelle
```
sudo apt update && sudo apt upgrade -y
```

## 2. Gereklileri yükle
```
sudo apt install wget jq build-essential nano unzip -y
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

## 5. Firewall ayarla
```
sudo apt install ufw -y
sudo ufw allow 30333
sudo ufw allow 30334
sudo ufw allow 9933
sudo ufw allow 9944
```

## 6. Astar Kur
```
cd $HOME
sudo mkdir /var/lib/astar
sudo useradd --no-create-home --shell /usr/sbin/nologin astar
sudo chown astar:astar /var/lib/astar
```

## 7. Dockerı çalıştır, ${NODE_NAME} değiştirmeyi unutma
```
docker run -d \
--name astar-container \
-u $(id -u astar):$(id -g astar) \
-p 30333:30333 \
-p 9933:9933 \
-p 9944:9944 \
-v "/var/lib/astar/:/data" \
staketechnologies/astar-collator:latest \
astar-collator \
--pruning archive \
--name ${NODE_NAME} \
--chain astar \
--execution Wasm \
--base-path /data \
--rpc-cors=all \
--unsafe-rpc-external \
--ws-external
```

## 8. node log kontrol
```
docker logs -f -n 100 $(docker ps -aq --filter name="astar-container")
```

## 9. Snapshot ve senkronizasyonu hızlandırma
```

```
