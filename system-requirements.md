# Hardware Requirements 

Official Docs: https://docs.0g.ai/run-a-node/overview

## Storage Node Hardware Requirements 

In the 0G network, storage nodes play a vital role in maintaining the system's decentralized storage layer. They are responsible for storing and serving data, ensuring data availability and reliability across the network. By running a storage node, you actively contribute to the network and earn rewards for your participation.
This guide details the process of running a storage node, including hardware specifications and interaction with on-chain contracts.

| Component | Storage Node | Storage KV |
|-----------|--------------|------------|
| Memory    | 16 GB RAM    | 4 GB RAM   |
| CPU       | 4 cores      | 4 cores    |
| Disk      | 500GB / 1TB NVMe SSD | Size matches the KV streams it maintains |
| Bandwidth | 100 Mbps (Download / Upload) | - |

:::note
- For Storage Node: The NVMe SSD ensures fast read/write operations, critical for efficient blob storage and retrieval.
- For Storage KV: The disk size requirement is flexible and should be adjusted based on the volume of KV streams you intend to maintain.

## Data Availability Node Hardware Requirements

To operate effectively, your DA signer needs to run a DA node to verify encoded blob data, sign it, and store it for future farming and rewards. Currently, to run a DA Node on Testnet, users must stake 10 A0GI tokens. These can be obtained through our [faucet](https://faucet.0g.ai/) or via rewards from running Storage Nodes or Validator Nodes. You can also reach out to our technical moderators on [Discord](https://discord.com/invite/0glabs).


| Node Type | Memory | CPU | Disk | Bandwidth | Additional Notes |
|-----------|--------|-----|------|-----------|------------------|
| DA Node   | 16 GB | 8 cores | 1 TB NVMe SSD | 100 MBps | For Download/Upload |
| DA Client | 4 GB | 2 cores | 50 MB HDD or SSD | 100 MBps | For Download/Upload |
| DA Retriever | 4 GB | 2 cores | 50 MB HDD or SSD | 100 MBps | For Download/Upload |

## Validator Node Hardware Requirements

Running a validator node in the 0G ecosystem means actively participating in the network's security and consensus through the Proof-of-Stake (PoS) mechanism. As a validator, you'll validate transactions, propose new blocks, and earn rewards for your contribution to the network's integrity and decentralization.

| Component  | Mainnet | Testnet |
|------------|---------|----------|
| Memory     | 64 GB   | 64 GB    |
| CPU        | 8 cores | 8 cores  |
| Disk       | 1 TB NVME SSD | 4 TB NVME SSD |
| Bandwidth  | 100 MBps for Download / Upload | 100 MBps for Download / Upload |


# Software Requirements

## Software Requirements for PC Users

1. For Windows Install WSL - https://learn.microsoft.com/en-us/windows/wsl/install#install-wsl-command

2. For Windows Install Ubuntu after Installing WSL - https://apps.microsoft.com/detail/9PDXGNCFSCZV?hl=en-us&gl=IN&ocid=pdpshare

3. For macOS If you have Installed Homebrew (https://brew.sh/) to manage packages on OS X,
run this command to install Git.
```
brew install git
```

4. Install Docker - https://www.docker.com/products/docker-desktop/

## Software Requirements for VPS Users (Additional Only for VPS Users to Download Docker)

```
sudo apt update -y
```
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
```
```
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
```
sudo apt update -y
```
```
apt-cache policy docker-ce
```
```
sudo apt install docker-ce -y
```
```
sudo usermod -aG docker ${USER}
su - ${USER}
groups
```
