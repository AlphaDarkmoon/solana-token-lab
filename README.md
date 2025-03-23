# Solana Token Creation Walkthrough

[Click Here](https://explorer.solana.com/address/mntjAooJxnwuzLeUnSxej39oJs9gTC12a2TH68KLQB9?cluster=devnet) to checkout my Coin.
## Requirements
- Docker
- Account on Phantom Wallet
- Account on Pinata.cloud

## Step 1: Create a Directory for Your Coin
Create a directory and name it after your coin (e.g., `byteFlux`).
```shell
mkdir byteFlux && cd byteFlux
```

## Step 2: Create a Dockerfile
Create a `Dockerfile` for setting up the environment.
```bash
nano Dockerfile
```

### Add the following content to the `Dockerfile`:
```dockerfile
# Use a lightweight base image
FROM debian:bullseye-slim

# Set non-interactive frontend for apt
ENV DEBIAN_FRONTEND=noninteractive

# Install required dependencies and Rust
RUN apt-get update && apt-get install -y \
    curl build-essential libssl-dev pkg-config nano \
    && curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Add Rust to PATH
ENV PATH="/root/.cargo/bin:$PATH"

# Verify Rust installation
RUN rustc --version

# Install Solana CLI
RUN curl -sSfL https://release.anza.xyz/stable/install | sh \
    && echo 'export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"' >> ~/.bashrc

# Add Solana CLI to PATH
ENV PATH="/root/.local/share/solana/install/active_release/bin:$PATH"

# Verify Solana CLI installation
RUN solana --version

# Set up Solana config for Devnet
RUN solana config set -ud

# Set working directory
WORKDIR /solana-token

# Default command to run a shell
CMD ["/bin/bash"]
```

## Step 3: Build the Docker Image
```bash
docker build -t byfx-sol .
```

## Step 4: Run the Docker Container
Replace `heysolana` with any container name of your choice.
```bash
docker run -it --rm -v $(pwd):/solana-token -v $(pwd)/solana-data:/root/.config/solana heysolana
```

## Step 5: Generate a Solana Key Pair
```bash
solana-keygen grind --starts-with dad:1
```
This generates a `.json` file that stores the private keys, e.g.,
```
dad2FSueEuGFYc43VFXjTV1VVhSgFEBQJdsB3XSyugx.json
```

## Step 6: Set the Generated Key Pair as Default
```bash
solana config set --keypair dad2FSueEuGFYc43VFXjTV1VVhSgFEBQJdsB3XSyugx.json
```

## Step 7: Switch to Devnet
```bash
solana config set --url devnet
```

## Step 8: Check Solana CLI Configuration
```bash
solana config get
```

## Step 9: Get Free SOL from Faucet
Visit [https://faucet.solana.com/](https://faucet.solana.com/) and paste your Solana address:
```bash
solana address
```

## Step 10: Create a Mint Address
```bash
solana-keygen grind --starts-with mnt:1
```
Example output:
```
mntjAooJxnwuzLeUnSxej39oJs9gTC12a2TH68KLQB9.json
```

## Step 11: Mint Your Token
```bash
spl-token create-token \
--program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb \
--enable-metadata \
--decimals 9 \
mnt-your-mint-address.json
```

### Example Output
```
Address: mntjAooJxnwuzLeUnSxej39oJs9gTC12a2TH68KLQB9
Decimals: 6
Signature: Hw2aeAv9hfDv3FxUE3eBeot2CXhRdNn59ViWfY1fFsmNyzFSfoAkSRJiBGsdBS9GPKCuwu3YEk8r4qaEGip3Vm7
```

## Step 12: Upload Token Image
Upload an image to [Pinata](https://pinata.cloud/) and copy the URL.
Example:
```
https://white-effective-hippopotamus-514.mypinata.cloud/ipfs/bafkreia3aev6tafrais2u33wpm4zjcd3nmm2tiqc6ockhtrkj6bk2ct5mm
```

## Step 13: Create Metadata File
```bash
nano metadata.json
```
Paste the following JSON:
```json
{
  "name": "Beard Coin",
  "symbol": "BEARD",
  "description": "Just a cool little coin, just for me.",
  "image": "https://salmon-effective-mosquito-301.mypinata.cloud/ipfs/bafkreigaiqsulww3fulikwx5ho2ifpfqfnco5bhqs4ui5gk5rn4ggkjuoy"
}
```
Upload `metadata.json` to Pinata and copy the URL.

## Step 14: Add Metadata to Token
```bash
spl-token initialize-metadata \
mntjAooJxnwuzLeUnSxej39oJs9gTC12a2TH68KLQB9 \
"Beard Coin" \
"BEARD" \
https://salmon-effective-mosquito-301.mypinata.cloud/ipfs/bafkreiamyf4f4iijqd2rp7phlqcdt4xoml2nfpeubavfyvwmi2vxpywawe
```

## Step 15: Create a Token Account
```bash
spl-token create-account mntjAooJxnwuzLeUnSxej39oJs9gTC12a2TH68KLQB9
```

## Step 16: Transfer Tokens to Another Wallet
```bash
spl-token transfer mntjAooJxnwuzLeUnSxej39oJs9gTC12a2TH68KLQB9 10 \
G5EibadVBKxnshP3NSFagJokfxXCD2VPm6WXoqgDSdQ2 --fund-recipient --allow-unfunded-recipient
```

### Example Output
```
Transfer 500 tokens
  Sender: Ey2yDTEnFk8nagrMHEe8FT9aqd6Q1aoTncKrxBCJrDcY
  Recipient: AqMssXD9kQJLNF2N6oHbMmdMUDAkD2zjefF5spgwtB9J
  Recipient associated token account: E4YCuTPHXtid4huH3891hJQDMtkS6gZCBzRTFvz6bWvB
Signature: qxCvNYo4g7qGhwhW3wcWV9x3eR3HaG2JhfczLcsLwwEoMwock4owFmCswfu5qUiRHDksXP3sMohUU7AFSedtiMc
```

## Conclusion
Congratulation! 🎉 You have just created a Coin of your own on SOL blockchain, remember, this is on devnet, so it can  only be transfer among friend's devnet accounts only.

---
