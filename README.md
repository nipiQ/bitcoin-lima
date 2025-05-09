# Bitcoin Core on macOS with Lima VM

This Lima VM template sets up a Bitcoin Core full node on macOS (Apple Silicon). It automates the installation of Bitcoin Core 29.0, verifies the binary's cryptographic signature and checksum for security, generates a random RPC password, and configures the node to run as a systemd service with a dedicated storage path.

## Prerequisites

- Lima (`brew install lima`)
- Storage path with ~600GB+ free space (e.g., `/Volumes/MyDrive`)

## Setup

1. Clone repository:

   ```bash
   git clone https://github.com/nipiQ/bitcoin-core-lima
   cd bitcoin-core-lima
   ```

2. Update `bitcoin-core.yaml`:

   - Replace all occurrences of `/Volumes/Storage` with your storage path (e.g., `/Volumes/MyDrive`).
   - Example macOS command to replace all instances in `bitcoin-core.yaml`:
     ```bash
     sed -i '' 's|/Volumes/Storage|/Volumes/MyDrive|g' bitcoin-core.yaml
     ```
   - Ensure your storage path exists and has sufficient space.

3. Create and start VM:

   ```bash
   limactl create --name bitcoin-core bitcoin-core.yaml
   limactl start bitcoin-core
   ```

4. Verify:

   ```bash
   limactl shell bitcoin-core
   bitcoin-cli getblockchaininfo
   ```

## Usage

- Run `bitcoin-cli getblockchaininfo` inside the VM to check node status.
- Ensure storage path is accessible before starting VM.
