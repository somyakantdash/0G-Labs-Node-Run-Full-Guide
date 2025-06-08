# Storage Node Run Full Guide (PC and VPS for Both)

### Offical Docs by 0G Labs - https://docs.0g.ai/run-a-node/storage-node

----

## 🧰 Prerequisites
### Before getting started, make sure you have the following:
	
Create New Metamask Wallet
-------
 * Add 0G-Galileo-Testnet: https://docs.0g.ai/run-a-node/testnet-information
 * Take Faucet: https://faucet.0g.ai/

---

1️⃣ Dependencies for WINDOWS & LINUX & VPS
```
sudo apt-get update && sudo apt-get upgrade -y
```
```
sudo apt install curl iptables build-essential git wget lz4 jq make cmake gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev screen ufw -y
```

2️⃣ Install Rustup
```
curl https://sh.rustup.rs -sSf | sh
```
```
source $HOME/.cargo/env
```
```
rustc --version
```


3️⃣ Install Go
```
wget https://go.dev/dl/go1.24.3.linux-amd64.tar.gz && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf go1.24.3.linux-amd64.tar.gz && \
rm go1.24.3.linux-amd64.tar.gz && \
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc && \
source ~/.bashrc
go version
```

For VPS Only
```
apt install screen -y
```
```
screen -S 0g-stoarge-node
```
- PRESS CTRL+A+D (to run ur node continuously)
- To check ur Node Again
```
screen -r 0g-stoarge-node
```

4️⃣ Clone the Repository
```
git clone https://github.com/0glabs/0g-storage-node.git
```
```
cd 0g-storage-node && git checkout v1.0.0 && git submodule update --init
```
```
cargo build --release
```

5️⃣ Set Configrations
```
rm -rf $HOME/0g-storage-node/run/config.toml
```
```
curl -o $HOME/0g-storage-node/run/config.toml https://raw.githubusercontent.com/somyakantdash/0G-Labs-Node-Run-Full-Guide/main/config.toml
```
```
nano $HOME/0g-storage-node/run/config.toml
```

* Go to "Log Sync Config Option" > Then Add ur any Public RPC
* Go to "Mine Config Option" > Put ur Wallet Private KEY in `miner-key` section ❗❗Dont Add **0X** before the key

![image](https://github.com/user-attachments/assets/a513812f-177e-4a74-83a9-1548c98f4556)

### If u want to Public RPC

1. Get any RPC - https://www.astrostake.xyz/0g-status

2. Chooose any rpc and edit in the `config.toml` file

![image](https://github.com/user-attachments/assets/44b682a5-45ce-4fc8-8c3a-7f2355f3b9ac)


6️⃣ Create a Systemd Service File
```
sudo tee /etc/systemd/system/zgs.service > /dev/null <<EOF
[Unit]
Description=ZGS Node
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/0g-storage-node/run
ExecStart=$HOME/0g-storage-node/target/release/zgs_node --config $HOME/0g-storage-node/run/config.toml
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

7️⃣ Start Node
```
sudo systemctl daemon-reload
```
```
sudo systemctl enable zgs
```
```
sudo systemctl start zgs
```


8️⃣ Managing Logs
```
sudo systemctl status zgs
```

![Screenshot 2025-05-27 190436](https://github.com/user-attachments/assets/3b01ab3f-8d43-43b3-9bf1-b2a8e870e1fe)


9️⃣ Check ur Full Logs
```
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d)
```

* Check block & Sync process - Match to the latest block on explorer

```
 while true; do     response=$(curl -s -X POST http://localhost:5678 -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"zgs_getStatus","params":[],"id":1}');     logSyncHeight=$(echo $response | jq '.result.logSyncHeight');     connectedPeers=$(echo $response | jq '.result.connectedPeers');     echo -e "logSyncHeight: \033[32m$logSyncHeight\033[0m, connectedPeers: \033[34m$connectedPeers\033[0m";     sleep 5; done
```

![Screenshot 2025-05-28 155703](https://github.com/user-attachments/assets/ab97078b-2c2a-4328-aace-bc94982ab802)

## 🔶For Next Day Run This Command

#1 Open WSL then Put this Command 
```
cd 0g-storage-node
sudo systemctl start zgs
```

## Stop & Delete Storage node
```
sudo systemctl stop zgs
```
```
sudo systemctl disable zgs
sudo rm /etc/systemd/system/zgs.service
rm -rf $HOME/0g-storage-node
```

## Check ur Txs (Put ur Address) - https://chainscan-galileo.bangcode.id/ OR https://chainscan-galileo.0g.ai/

## Check ur Miner Details (Add your wallet address at the end of the link) - https://storagescan-galileo.0g.ai/miner/

