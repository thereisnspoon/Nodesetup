```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt install curl tar wget clang pkg-config git make libssl-dev libclang-dev libclang-12-dev -y
```
```
sudo apt install jq build-essential bsdmainutils ncdu gcc git-core chrony liblz4-tool -y
```
```
sudo apt install uidmap dbus-user-session protobuf-compiler unzip -y
```

```
if ! [ -x "$(command -v go)" ]; then
  ver="1.20.5"
  cd $HOME
  wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
  sudo rm -rf /usr/local/go
  sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
  rm "go$ver.linux-amd64.tar.gz"
  echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
  source ~/.bash_profile
fi
```
```
git clone https://github.com/gateway-fm/polygon-edge.git
```
```
cd polygon-edge
```
```
git checkout palm-migration
make build
```
```
cd
screen -S snap
wget https://pub-c5ad1adfc7a74a99a19b1c7f3d621fa8.r2.dev/palm-mainnet.tar.gz
```
```
tar -xvf palm-mainnet.tar.gz
```
```
cd palm-data
./polygon-edge polybft-secrets --insecure --data-dir="/root/palm-data"
```
```
nano /root/palm-data/config.toml
```
```
[jsonrpc]

  [jsonrpc.http]
    enabled = true
    port = 8545
    host = "0.0.0.0"
    api = ["eth", "net", "web3", "admin", "ibft", "txpool", "bor", "debug", "trace"]

  [jsonrpc.ws]
    enabled = true
    port = 8545
    host = "0.0.0.0"
    api = ["eth", "net", "web3", "admin", "ibft", "txpool", "bor", "debug", "trace"]
```
```
sudo tee <<EOF > /dev/null /etc/systemd/system/palm.service
[Unit]
Description=Palm
After=network.target
StartLimitIntervalSec=0
[Service]
Type=simple
Restart=on-failure
RestartSec=10
User=$USER
KillSignal=SIGHUP
ExecStart=$HOME/datadir/palm-data/polygon-edge server \
--chain="$HOME/datadir/palm-data/genesis.json" \
--data-dir="$HOME/datadir/palm-data" \
--libp2p "0.0.0.0:30301" \
--devp2p "0.0.0.0:30302"  \
--jsonrpc="0.0.0.0:8545" \
--grpc-address="0.0.0.0:9632"
[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl enable palm
sudo systemctl daemon-reload
sudo systemctl start palm
```
```
sudo journalctl -fo cat -u palm
```
```
sudo systemctl stop palm
```


```
http://IP:8545
ws://IP:8545/ws
```
