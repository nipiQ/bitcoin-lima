# Bitcoin Full Node on macOS with Lima VM

This repository provides Lima VM templates to run a Bitcoin full node on macOS (Apple Silicon) using either Bitcoin Core (v29.0) or Bitcoin Knots (v28.1.knots20250305). Both clients automate installation, verify binary signatures and checksums for security, generate a random RPC password, and configure the node as a systemd service with a dedicated storage path. Bitcoin Knots, a fork of Bitcoin Core, includes additional features like enhanced mempool policies.

⚠️ **Warning**: Due to slow download speeds, I haven’t extensively tested `bitcoin-knots.yaml`. I’ll revisit it when time allows.

## Prerequisites

- Lima (`brew install lima`)
- Storage path with ~600GB+ free space (e.g., `/Volumes/MyDrive`)

## Setup

1. Clone repository:

   ```bash
   git clone https://github.com/nipiQ/bitcoin-lima
   cd bitcoin-lima
   ```

2. Choose a template:

   - For Bitcoin Core: Use `bitcoin-core.yaml`.
   - For Bitcoin Knots: Use `bitcoin-knots.yaml`.

3. Update the template:

   - Replace all occurrences of `/Volumes/Storage` with your storage path (e.g., `/Volumes/MyDrive`).
   - Example macOS command to replace all instances:
     ```bash
     sed -i '' 's|/Volumes/Storage|/Volumes/MyDrive|g' <template-file>
     ```
     Replace `<template-file>` with `bitcoin-core.yaml` or `bitcoin-knots.yaml`.
   - Ensure your storage path exists and has sufficient space.

4. Create and start VM:

   ```bash
   limactl create --name <vm-name> <template-file>
   limactl start <vm-name>
   ```

   Replace `<vm-name>` with `bitcoin-core` or `bitcoin-knots`, and `<template-file>` with the chosen template.

   - Note: Bitcoin Knots downloads may be slow; allow extra time for `limactl start` to complete.

5. Verify:

   ```bash
   limactl shell <vm-name>
   bitcoin-cli getblockchaininfo
   ```

## Usage

- Run `bitcoin-cli getblockchaininfo` inside the VM to check node status.
- Ensure storage path is accessible before starting VM.
