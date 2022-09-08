# CW20-ERC20-Tutorial

This will take you through uploading your own CW-20/ERC-20 to the Source Chain testnet.
It uses the examples in the contracts/erc20 folder of the cosmwasm-examples repo.
There are four steps involved in working with a smart contract.

- Write the smart contract (already done here!)
- Store the smart contract on chain
- Instantiate the smart contract (configure and initialise it)
- Execute commands provided by the smart contract

We will go through all of these in this tutorial.


### INSTALLING RUST

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Once installed, make sure you have the wasm32 target:

```
rustup default stable
cargo version
# If this is lower than 1.49.0+, update
rustup update stable

rustup target list --installed
rustup target add wasm32-unknown-unknown
```

### DOWNLOAD
