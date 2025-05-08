# Bitcoin Core on macOS with Lima VM

Run a Bitcoin Core full node on macOS (Apple Silicon) using Lima VM.

## Prerequisites

- Lima (`brew install lima`)
- Storage path with ~600GB+ free space (e.g., `/Volumes/Storage`)

## Setup

1. Clone repository:

   ```bash
   git clone https://github.com/nipiQ/bitcoin-core-lima
   cd bitcoin-core-lima
   ```

2. Update `bitcoin-core.yaml`:

   - Replace `/Volumes/Storage` with your storage path.

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
