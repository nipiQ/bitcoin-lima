<p align="center"><img alt="Bitcoin Lima banner" title="Bitcoin Development Toolkit for macOS with Lima VMs." src="https://raw.githubusercontent.com/nipiQ/.github/main/assets/bitcoin-lima-repo-banner.webp" width="100%">
</p>

# Bitcoin Development Toolkit for macOS with Lima VMs

This repository provides Lima VM templates to run Bitcoin full nodes on macOS (Apple Silicon) using Bitcoin Core (v29.0) for mainnet, testnet3, and testnet4, as well as Bitcoin Knots (v28.1.knots20250305) for mainnet only. Each template runs in its own isolated Lima VM, allowing you to operate multiple networks simultaneously without conflicts.

All templates automate installation, verify binary signatures and checksums for security, generate a random RPC password, and configure the node as a systemd service with a dedicated storage path. The macOS host never runs any bitcoind instances directly; all nodes run inside their respective Lima VMs for maximum isolation and safety.

## Prerequisites

- Lima (`brew install lima`)
- ChainData path with ~600GB+ free space (e.g., `/Volumes/MyDrive`)
- `bitcoin-cli` installed on macOS host (`brew install bitcoin`)

## Setup

1. Clone repository:

   ```bash
   git clone https://github.com/nipiQ/bitcoin-lima
   cd bitcoin-lima
   ```

2. Choose a template:

   - For Bitcoin Core mainnet: Use `bitcoin-core.yaml`.
   - For Bitcoin Core testnet3: Use `bitcoin-testnet3.yaml`.
   - For Bitcoin Core testnet4: Use `bitcoin-testnet4.yaml`.
   - For Bitcoin Knots mainnet: Use `bitcoin-knots.yaml` (mainnet only).

3. Update the template:

   - Replace all occurrences of `/Volumes/ChainData` with your storage path (e.g., `/Volumes/MyDrive`).
   - Example macOS command to replace all instances:
     ```bash
     sed -i '' 's|/Volumes/ChainData|/Volumes/MyDrive|g' <template-file>
     ```
     Replace `<template-file>` with `bitcoin-core.yaml`, `bitcoin-testnet3.yaml`, `bitcoin-testnet4.yaml`, or `bitcoin-knots.yaml`.
   - Ensure your storage path exists and has sufficient space.

4. Create and start VM:

   ```bash
   limactl create --name <vm-name> <template-file>
   limactl start <vm-name>
   ```

   Replace `<vm-name>` with `bitcoin-core`, `bitcoin-testnet3`, `bitcoin-testnet4`, or `bitcoin-knots`, and `<template-file>` with the chosen template.

5. Verify:

   ```bash
   limactl shell <vm-name>
   bitcoin-cli getblockchaininfo
   ```

## Reusing Blockchain Data (Advanced)

> **Note for Bitcoin Knots users**: If you already have a fully synced Bitcoin Core node, you can optionally reuse the blockchain data to avoid downloading it again with Bitcoin Knots.

This is completely optional but can save significant time and bandwidth. **Proceed with caution and always keep blockchains in separate directories**:

1. Create a dedicated directory for Bitcoin Knots:
   ```bash
   mkdir -p /Volumes/ChainData/bitcoin-knots
   ```

2. Copy the blockchain data from your Bitcoin Core directory:
   ```bash
   cp -r /Volumes/ChainData/bitcoin-core/blocks /Volumes/ChainData/bitcoin-knots/
   cp -r /Volumes/ChainData/bitcoin-core/chainstate /Volumes/ChainData/bitcoin-knots/
   ```
   
3. Delete the index directory to force reindexing (much faster than downloading):
   ```bash
   rm -rf /Volumes/ChainData/bitcoin-knots/blocks/index
   ```

4. Start your Bitcoin Knots node normally using the template. It will automatically rebuild the index from the existing blocks.

This approach ensures that Bitcoin Core and Bitcoin Knots each use their own separate directories, avoiding conflicts while still benefiting from reusing the downloaded blockchain data.

## Example bitcoin.conf for bitcoin-cli and development

The macOS host never runs bitcoind directly. Instead, you can use `bitcoin-cli` (installed via Homebrew) to interact with your nodes running inside the Lima VMs. For development or scripting, you may want a `"~/Library/Application Support/Bitcoin/bitcoin.conf"` file on your macOS host to define multiple networks and RPC credentials. Here is an example:

```ini
[main]
txindex=1
server=1
rpcport=8332
rpcuser=bitcoinuser
rpcconnect=127.0.0.1
rpcpassword=strongpassword

[test]
txindex=1
server=1
rpcport=18332
rpcbind=127.0.0.1
rpcallowip=127.0.0.1
rpcuser=bitcoinuser
rpcpassword=strongpassword
testnet=1

[testnet4]
txindex=1
server=1
rpcbind=127.0.0.1
rpcport=18443
rpcallowip=127.0.0.1
rpcuser=bitcoinuser
rpcpassword=strongpassword
testnet4=1
```

Adjust the `rpcpassword` and `rpcport` values to match those generated in your VM's `bitcoin.conf` files. This setup allows you to use `bitcoin-cli` on your macOS host to connect to any of your running nodes for mainnet, testnet3, or testnet4.

## Usage

- Run one of the following inside the VM to check node status:
  - `bitcoin-cli getblockchaininfo` (mainnet)
  - `bitcoin-cli -testnet getblockchaininfo` (testnet3)
  - `bitcoin-cli -testnet4 getblockchaininfo` (testnet4)
- Or, use `bitcoin-cli` from your macOS host with the appropriate `-conf` or `-rpc*` flags to connect to your VM nodes.
- Ensure storage path is accessible before starting VM.

## Connect

Follow me on X: [@nipiQ](https://x.com/nipiQ).
