# Bitcoin Development Toolkit for macOS with Lima VMs (Core Nodes, Layer 2, BTCFi)

This repository provides Lima VM templates to run Bitcoin full nodes on macOS (Apple Silicon) using Bitcoin Core (v29.0) for mainnet, testnet3, and testnet4. Each template runs in its own isolated Lima VM, allowing you to operate multiple networks simultaneously without conflicts. All templates automate installation, verify binary signatures and checksums for security, generate a random RPC password, and configure the node as a systemd service with a dedicated storage path. The macOS host never runs any bitcoind instances directly; all nodes run inside their respective Lima VMs for maximum isolation and safety.

## Prerequisites

- Lima (`brew install lima`)
- Storage path with ~600GB+ free space (e.g., `/Volumes/MyDrive`)
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

3. Update the template:

   - Replace all occurrences of `/Volumes/Storage` with your storage path (e.g., `/Volumes/MyDrive`).
   - Example macOS command to replace all instances:
     ```bash
     sed -i '' 's|/Volumes/Storage|/Volumes/MyDrive|g' <template-file>
     ```
     Replace `<template-file>` with `bitcoin-core.yaml`, `bitcoin-testnet3.yaml`, or `bitcoin-testnet4.yaml`.
   - Ensure your storage path exists and has sufficient space.

4. Create and start VM:

   ```bash
   limactl create --name <vm-name> <template-file>
   limactl start <vm-name>
   ```

   Replace `<vm-name>` with `bitcoin-core`, `bitcoin-testnet3`, or `bitcoin-testnet4`, and `<template-file>` with the chosen template.

5. Verify:

   ```bash
   limactl shell <vm-name>
   bitcoin-cli getblockchaininfo
   ```

## Example bitcoin.conf for bitcoin-cli and development

The macOS host never runs bitcoind directly. Instead, you can use `bitcoin-cli` (installed via Homebrew) to interact with your nodes running inside the Lima VMs. For development or scripting, you may want a `~/.bitcoin/bitcoin.conf` file on your macOS host to define multiple networks and RPC credentials. Here is an example:

```ini
[main]
rpcuser=bitcoinuser
rpcpassword=your_mainnet_rpc_password
rpcconnect=127.0.0.1
rpcport=8332

[test]
testnet=1
rpcuser=bitcoinuser
rpcpassword=your_testnet3_rpc_password
rpcconnect=127.0.0.1
rpcport=18332

[testnet4]
testnet=4
rpcuser=bitcoinuser
rpcpassword=your_testnet4_rpc_password
rpcconnect=127.0.0.1
rpcport=28332
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
