# Data Availability Node Run Full Guide (PC and VPS for Both)

### Offical Docs by 0G Labs - https://docs.0g.ai/run-a-node/da-node

## Data Availability Client Node (DA Client Node)

1️⃣ Dependencies for WINDOWS & LINUX & VPS
```
sudo apt update
sudo apt upgrade -y
```

For VPS Only
```
apt install screen -y
```

2️⃣ Download Some Files
```
git clone https://github.com/0glabs/0g-da-client.git
```

For VPS Only
```
screen -S da client
```

3️⃣ Build Docker Image
```
cd 0g-da-client
docker build -t 0g-da-client -f combined.Dockerfile .
```

4️⃣ Create File 
```
nano envfile.env
```

5️⃣ Copy & Paste the following code in it
Replace PRIVATE_KEY with your Actual Wallet Private Key
```
# envfile.env
COMBINED_SERVER_CHAIN_RPC=https://evmrpc-testnet.0g.ai
COMBINED_SERVER_PRIVATE_KEY=PRIVATE_KEY
ENTRANCE_CONTRACT_ADDR=0x857C0A28A8634614BB2C96039Cf4a20AFF709Aa9

COMBINED_SERVER_RECEIPT_POLLING_ROUNDS=180
COMBINED_SERVER_RECEIPT_POLLING_INTERVAL=1s
COMBINED_SERVER_TX_GAS_LIMIT=2000000
COMBINED_SERVER_USE_MEMORY_DB=true
COMBINED_SERVER_KV_DB_PATH=/runtime/
COMBINED_SERVER_TimeToExpire=2592000
DISPERSER_SERVER_GRPC_PORT=51001
BATCHER_DASIGNERS_CONTRACT_ADDRESS=0x0000000000000000000000000000000000001000
BATCHER_FINALIZER_INTERVAL=20s
BATCHER_CONFIRMER_NUM=3
BATCHER_MAX_NUM_RETRIES_PER_BLOB=3
BATCHER_FINALIZED_BLOCK_COUNT=50
BATCHER_BATCH_SIZE_LIMIT=500
BATCHER_ENCODING_INTERVAL=3s
BATCHER_ENCODING_REQUEST_QUEUE_SIZE=1
BATCHER_PULL_INTERVAL=10s
BATCHER_SIGNING_INTERVAL=3s
BATCHER_SIGNED_PULL_INTERVAL=20s
BATCHER_EXPIRATION_POLL_INTERVAL=3600
BATCHER_ENCODER_ADDRESS=DA_ENCODER_SERVER
BATCHER_ENCODING_TIMEOUT=300s
BATCHER_SIGNING_TIMEOUT=60s
BATCHER_CHAIN_READ_TIMEOUT=12s
BATCHER_CHAIN_WRITE_TIMEOUT=13s
```

Then save - CTRL+X Then Enter Y Then Enter

6️⃣ Start Node
```
docker run -d --env-file envfile.env --name 0g-da-client -v ./run:/runtime -p 51001:51001 0g-da-client combined
```

7️⃣ Check your Node Status
```
docker ps
```

8️⃣ View Your Logs
```
docker logs -f 0g-da-client
```

### 🔶For Next Day Run This Command

#1 Open WSL and Docker
```
cd 0g-da-client
docker run -d --env-file envfile.env --name 0g-da-client -v ./run:/runtime -p 51001:51001 0g-da-client combined
```

### Delete DA Client node
```
docker stop 0g-da-client
docker rm 0g-da-client
rm -rf $HOME/0g-da-client
```

## Data Availability Node (DA Node)

1️⃣ Dependencies for WINDOWS & LINUX & VPS
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make gcc tar clang pkg-config libssl-dev ncdu protobuf-compiler -y
```

2️⃣ Install Go & Rust
```
cd $HOME
VER="1.22.0"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
go version
```
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
- Then Just Press Enter

3️⃣ Download Some Files
```
cd $HOME
rm -rf 0g-da-node
git clone https://github.com/0glabs/0g-da-node.git
cd 0g-da-node
git fetch --all --tag
git checkout v1.1.3
git submodule update --init
cargo build --release
./dev_support/download_params.sh
```

Generate BLS private key, if you don't have one. It will register the signer information in DA contract when you first run DA node.
❗NOTE: Keep the key safe
```
cargo run --bin key-gen
```

3️⃣ Create File
```
nano $HOME/0g-da-node/config.toml
```

4️⃣ Copy & Paste the following code in it (Make sure to fill in the socket_address, signer_bls_private_key, signer_eth_private_key, and miner_eth_private_key fields with your actual private keys)
```
log_level = "info"

data_path = "./db/"

# path to downloaded params folder
encoder_params_dir = "params/" 

# grpc server listen address
grpc_listen_address = "0.0.0.0:34000"
# chain eth rpc endpoint
eth_rpc_endpoint = "https://evmrpc-testnet.0g.ai"
# public grpc service socket address to register in DA contract
# ip:34000 (keep same port as the grpc listen address)
# or if you have dns, fill your dns
socket_address = "<public_ip/dns>:34000"

# data availability contract to interact with
da_entrance_address = "0x857C0A28A8634614BB2C96039Cf4a20AFF709Aa9"
# deployed block number of da entrance contract
start_block_number = 940000

# signer BLS private key
signer_bls_private_key = ""
# signer eth account private key
signer_eth_private_key = ""
# miner eth account private key, (could be the same as 'signer_eth_private_key', but not recommended)
miner_eth_private_key = ""

# whether to enable data availability sampling
enable_das = "true"
```
### Socket Address (for PC)
```
curl ifconfig.me
```

Then save - CTRL+X Then Enter Y Then Enter

5️⃣ Start Node
```
docker build -t 0g-da-node .
docker run -d --name 0g-da-node 0g-da-node
```

6️⃣ Check Your Logs
```
sudo journalctl -u 0gda -f -o cat
```

### 🔶For Next Day Run This Command

#1 Open WSL and Docker
```
cd 0g-da-node
docker run -d --name 0g-da-node 0g-da-node
```

### Delete DA node
```
docker stop 0g-da-node
sudo rm /etc/systemd/system/0gda.service
rm -rf $HOME/0g-da-node
```

## Data Availability Retriever Node (DA Retriever Node)

1️⃣ Dependencies for WINDOWS & LINUX & VPS
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make gcc tar clang pkg-config libssl-dev ncdu protobuf-compiler -y
```

For VPS Only
```
apt install screen -y
```

2️⃣ Install Rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

3️⃣ Download Some Files
```
cd $HOME
rm -rf 0g-da-retriever
git clone https://github.com/0glabs/0g-da-retriever.git
cd 0g-da-retriever
```

4️⃣ Build in release mode
```
cargo build --release
```

5️⃣ Update configuration
```
nano $HOME/0g-da-retriever/run/config.toml
```

6️⃣ Sample
```
log_level = "info"
grpc_listen_address = "0.0.0.0:34005"

#YOUR_RPC_ENDPOINT:
eth_rpc_endpoint = "https://evmrpc-testnet.0g.ai" 
```

Then save - CTRL+X Then Enter Y Then Enter

7️⃣ Create Service file
```
sudo tee /etc/systemd/system/0gdar.service > /dev/null <<EOF
[Unit]
Description=0G-DA Retriever
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/0g-da-retriever
ExecStart=$HOME/0g-da-retriever/target/release/retriever --config $HOME/0g-da-retriever/run/config.toml
Restart=always
RestartSec=10
LimitNOFILE=65535
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
EOF
```

7️⃣ Start Node
```
sudo systemctl daemon-reload && sudo systemctl enable 0gdar && sudo systemctl start 0gdar && sudo systemctl status 0gdar && sudo journalctl -u 0gdar -f -o cat
docker ps
```

8️⃣ View Your Logs
```
journalctl -u 0gdar -f -o cat
```

### 🔶For Next Day Run This Command

#1 Open WSL and Docker
```
cd 0g-da-retriever
sudo systemctl daemon-reload && sudo systemctl enable 0gdar && sudo systemctl start 0gdar && sudo systemctl status 0gdar && sudo journalctl -u 0gdar -f -o cat
```

### Delete DA Retriever node
```
sudo systemctl stop 0gdar
sudo systemctl disable 0gdar
sudo rm /etc/systemd/system/0gdar.service
rm -rf $HOME/0g-da-retriever
```
