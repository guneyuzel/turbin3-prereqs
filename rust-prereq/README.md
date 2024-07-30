# Solana Wallet and Transaction Utilities

This repository contains a collection of utilities for working with Solana wallets and transactions. It includes functions for generating keypairs, converting between different wallet formats, airdropping SOL tokens, transferring SOL, and signing transactions with a custom program.

## Table of Contents

- Installation
- Usage
  - [Generating a Keypair](#generating-a-keypair)
  - [Converting Wallet to Base58](#converting-wallet-to-base58)
  - [Converting Base58 to Wallet](#converting-base58-to-wallet)
  - [Airdropping SOL](#airdropping-sol)
  - [Transferring SOL](#transferring-sol)
  - [Signing with WBA Program](#signing-with-wba-program)
- Testing
- License

## Installation

To use this repository, you need to have Rust installed. You can install Rust using [rustup](https://rustup.rs/).

Clone the repository:

```sh
git clone <url>
cd rust-prereq
```

## Usage

### Generating a Keypair

To generate a new Solana keypair, use the `keygen` function:

```rust
fn keygen() {
    let kp = Keypair::new();
    println!(
        "You've generated a new Solana wallet: {}",
        kp.pubkey().to_string()
    );
    println!("");
    println!("To save your wallet, copy and paste the following into a JSON file:");
    println!("{:?}", kp.to_bytes());
}
```

### Converting Wallet to Base58

To convert a wallet file byte array to a Base58 string, use the [`wallet_to_base58`](command:_github.copilot.openSymbolFromReferences?%5B%7B%22%24mid%22%3A1%2C%22path%22%3A%22%2Fhome%2Fdevtex%2FCodeUbuntu%2Fturbin3-prereqs%2Frust-prereq%2Fsrc%2Flib.rs%22%2C%22scheme%22%3A%22file%22%7D%2C%7B%22line%22%3A35%2C%22character%22%3A4%7D%5D "rust-prereq/src/lib.rs") function:

```rust
fn wallet_to_base58() {
    println!("Input your private key as a wallet file byte array:");
    let stdin = io::stdin();
    let wallet = stdin
        .lock()
        .lines()
        .next()
        .unwrap()
        .unwrap()
        .trim_start_matches('[')
        .trim_end_matches(']')
        .split(',')
        .map(|s| s.trim().parse::<u8>().unwrap())
        .collect::<Vec<u8>>();
    println!("Your private key is:");
    let base58 = bs58::encode(wallet).into_string();
    println!("{:?}", base58);
}
```

### Converting Base58 to Wallet

To convert a Base58 string to a wallet file byte array, use the `base58_to_wallet` function:

```rust
fn base58_to_wallet() {
    println!("Input your private key as base58:");
    let stdin = io::stdin();
    let base58 = stdin.lock().lines().next().unwrap().unwrap();
    println!("Your wallet file is:");
    let wallet = bs58::decode(base58).into_vec().unwrap();
    println!("{:?}", wallet);
}
```

### Airdropping SOL

To airdrop SOL tokens to a wallet, use the [`airdrop`](command:_github.copilot.openRelativePath?%5B%7B%22scheme%22%3A%22file%22%2C%22authority%22%3A%22%22%2C%22path%22%3A%22%2Fhome%2Fdevtex%2FCodeUbuntu%2Fturbin3-prereqs%2Fairdrop%22%2C%22query%22%3A%22%22%2C%22fragment%22%3A%22%22%7D%5D "/home/devtex/CodeUbuntu/turbin3-prereqs/airdrop") function:

```rust
fn airdrop() {
    let keypair = read_keypair_file("dev-wallet.json").expect("Couldn't find wallet file");
    let client = RpcClient::new(RPC_URL);
    match client.request_airdrop(&keypair.pubkey(), 2_000_000_000u64) {
        Ok(s) => {
            println!("Success! Check out your TX here:");
            println!(
                "https://explorer.solana.com/tx/{}?cluster=devnet",
                s.to_string()
            );
        }
        Err(e) => println!("Oops, something went wrong: {}", e.to_string()),
    };
}
```

### Transferring SOL

To transfer SOL tokens from one wallet to another, use the `transfer_sol` function:

```rust
fn transfer_sol() {
    let keypair = read_keypair_file("dev-wallet.json").expect("Couldn't find wallet file");
    let to_pubkey = Pubkey::from_str("EyQiDRP7Pz4w6G7viieZyQCJPT7F2kbiEz

f

JsM1tMaJ7").unwrap();
    let rpc_client = RpcClient::new(RPC_URL);
    let recent_blockhash = rpc_client
        .get_latest_blockhash()
        .expect("Failed to get recent blockhash");
    let balance = rpc_client
        .get_balance(&keypair.pubkey())
        .expect("Failed to get balance");
    let message = Message::new_with_blockhash(
        &[transfer(&keypair.pubkey(), &to_pubkey, balance)],
        Some(&keypair.pubkey()),
        &recent_blockhash,
    );
    let fee = rpc_client
        .get_fee_for_message(&message)
        .expect("Failed to get fee calculator");
    let transaction = Transaction::new_signed_with_payer(
        &[transfer(&keypair.pubkey(), &to_pubkey, balance - fee)],
        Some(&keypair.pubkey()),
        &vec![&keypair],
        recent_blockhash,
    );
    let signature = rpc_client
        .send_and_confirm_transaction(&transaction)
        .expect("Failed to send transaction");
    println!(
        "Success! Check out your TX here:
    https://explorer.solana.com/tx/{}/?cluster=devnet",
        signature
    );
}
```

### Signing with WBA Program

To sign a transaction with the WBA program, use the [`wbasign`](command:_github.copilot.openSymbolFromReferences?%5B%7B%22%24mid%22%3A1%2C%22path%22%3A%22%2Fhome%2Fdevtex%2FCodeUbuntu%2Fturbin3-prereqs%2Frust-prereq%2Fsrc%2Flib.rs%22%2C%22scheme%22%3A%22file%22%7D%2C%7B%22line%22%3A119%2C%22character%22%3A4%7D%5D "rust-prereq/src/lib.rs") function:

```rust
fn wbasign() {
    let rpc_client = RpcClient::new(RPC_URL);
    let signer = read_keypair_file("dev-wallet.json").expect("Couldn't find wallet file");
    let prereq = WbaPrereqProgram::derive_program_address(&[
        b"prereq",
        signer.pubkey().to_bytes().as_ref(),
    ]);
    let args = CompleteArgs {
        github: b"guneyuzel".to_vec(),
    };
    let blockhash = rpc_client
        .get_latest_blockhash()
        .expect("Failed to get recent blockhash");
    let transaction = WbaPrereqProgram::complete(
        &[&signer.pubkey(), &prereq, &system_program::id()],
        &args,
        Some(&signer.pubkey()),
        &[&signer],
        blockhash,
    );
    let signature = rpc_client
        .send_and_confirm_transaction(&transaction)
        .expect("Failed to send transaction");
    println!(
        "Success! Check out your TX here: https://explorer.solana.com/tx/{}/?cluster=devnet",
        signature
    );
}
```

## Testing

To run the tests, use the following command:

```sh
cargo test
```