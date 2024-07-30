# Bridge to Turbin3

This repository contains a collection of TypeScript scripts designed to interact with the Solana blockchain. Each script serves a specific purpose in the development and testing of Solana programs (smart contracts) and transactions. Below is a brief overview of each script and its functionality.

## Final Transaction

View the final transaction on the Solana Explorer: [Final Transaction](https://explorer.solana.com/tx/5LHNRvDGXhVM66wbHuPoz7ozhEbareifLTcMJvkoEuBjHe36QzVUCDMwUu3P3srtgAwr8rQ9mE1WdxeWDiryUWiQ?cluster=devnet)

## Scripts Overview

1. **wba_prereq.ts** (WBA_PREREQ.TS CONTEXT)
    It includes metadata, instructions for completing and updating prerequisites, account information, error codes, and types for the application.

2. **keygen.ts** (KEYGEN.TS CONTEXT)
   Generates a new Solana wallet keypair and prints the public and secret keys to the console. This script is essential for creating a new wallet for development purposes.

3. **enroll.ts** (ENROLL.TS CONTEXT)
   Handles the enrollment process for the WBA application. It uses the @coral-xyz/anchor framework to interact with the Solana program defined in wba_prereq.ts. The script demonstrates how to connect to Solana, send a transaction, and handle the response.

4. **transfer.ts** (TRANSFER.TS CONTEXT)
   Facilitates the transfer of SOL (Solana's native cryptocurrency) from one wallet to another. It showcases the creation of a Solana transaction, adding a transfer instruction, and sending the transaction to the network.

5. **airdrop.ts** (AIRDROP.TS CONTEXT)
   Requests an airdrop of SOL to a specified wallet. This script is useful for obtaining test SOL on the Devnet for development and testing purposes.

# Getting Started

Follow these steps to set up and run the project:

1. **Install Dependencies**

   Open your terminal and navigate to the project directory. Run the following command to install the necessary dependencies:
   ```bash
   npm install
2. **Create a Dev Wallet**

   In the root directory of your project, create a file named dev-wallet.json. Add your Solana wallet secret key to this file.


3. **Run the Project**
   Use the following command to run the desired file:
    ```bash
   npm run <desired-file>