# Building Bitcoin in Rust

[![Ask DeepWiki](https://devin.ai/assets/askdeepwiki.png)](https://deepwiki.com/Aditaya08/Building_bitcoin_in_rust)

This repository is a Rust implementation of the fundamental components of a Bitcoin-like cryptocurrency. It serves as an educational project to understand the inner workings of a blockchain, including data structures, cryptographic principles, and consensus rules.

The project is structured as a Cargo workspace, separating the core logic from the different node applications (miner, wallet, etc.).

## Workspace Structure

The repository is organized into several crates:

*   **`lib`**: The core library that contains all the essential data structures and logic for the blockchain. This includes definitions for blocks, transactions, cryptographic functions, and validation rules.
*   **`miner`**: (WIP) A binary crate intended for the mining node, which will be responsible for creating new blocks.
*   **`node`**: (WIP) A binary crate for a full blockchain node, which will handle network communication, block synchronization, and transaction propagation.
*   **`wallet`**: (WIP) A binary crate for a user wallet, used for managing private/public keys and creating transactions.

## Core Features (in `lib`)

The `lib` crate implements the foundational elements of the blockchain:

### Data Structures

*   **`Blockchain`**: The main container for the chain of blocks and the current set of Unspent Transaction Outputs (UTXOs).
*   **`Block` & `BlockHeader`**: Represents a block in the chain, containing a header with metadata (timestamp, nonce, previous hash, etc.) and a list of transactions.
*   **`Transaction`**: A structure representing a transfer of value, composed of inputs and outputs.
*   **`TransactionInput`**: Points to a previous `TransactionOutput` and provides a digital signature to prove ownership.
*   **`TransactionOutput`**: A specific amount of currency locked to a public key, which can be spent in a future transaction.

### Cryptography

*   **SHA-256 Hashing**: Custom `Hash` type that uses SHA-256 to generate unique identifiers for transactions and blocks. Serialization is handled by `ciborium` before hashing.
*   **Public/Private Key Cryptography**: Uses the `k256` crate for `secp256k1` elliptic curve cryptography, the same curve used by Bitcoin.
    *   **`PrivateKey`**: Used for signing transactions to authorize spending.
    *   **`PublicKey`**: Used to verify signatures and as the recipient "address" in transaction outputs.
    *   **`Signature`**: The digital signature that proves ownership of the funds being spent.

### Consensus and Validation Logic

*   **Proof of Work**: Includes logic to verify that a block's hash meets a given `target` difficulty (`Hash::matches_target`).
*   **Merkle Trees**: Implements Merkle root calculation (`MerkleRoot::calculate`) to efficiently verify that all transactions are included in a block without having to hash all of them.
*   **Block Validation**: The `Blockchain::add_block` method enforces rules such as:
    *   The `prev_block_hash` must match the hash of the previous block.
    *   The block's hash must be below the difficulty target.
    *   The Merkle root must be correctly calculated from the block's transactions.
    *   The timestamp must be later than the previous block's.
*   **Transaction Validation**: The `Block::verify_transactions` method ensures:
    *   Signatures on inputs are valid.
    *   Inputs have not already been spent (preventing double-spending).
    *   The total value of inputs is greater than or equal to the total value of outputs (the remainder becomes the miner's fee).
*   **Coinbase Transactions & Rewards**:
    *   Calculates the correct block reward based on the block height, including reward halving (`HALVING_INTERVAL`).
    *   Accurately calculates the total transaction fees for the miner.
    *   Validates that the coinbase transaction (the first transaction in a block) pays the miner the correct sum of the block reward and transaction fees.

## Getting Started

### Prerequisites

*   [Rust](https://www.rust-lang.org/tools/install) programming language and Cargo package manager.

### Building the Project

1.  Clone the repository:
    ```sh
    git clone https://github.com/aditaya08/building_bitcoin_in_rust.git
    ```

2.  Navigate to the project directory:
    ```sh
    cd building_bitcoin_in_rust
    ```

3.  Build the entire workspace:
    ```sh
    cargo build
    ```

This will compile the `lib` crate and the placeholder binaries for the `miner`, `node`, and `wallet`. As development progresses, these binaries will become functional applications.
