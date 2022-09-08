# CW20-ERC20-Tutorial

This will take you through uploading your own CW-20/ERC-20 to the Source Chain testnet.
It uses the examples in the contracts/erc20 folder of the [cosmwasm-examples](https://github.com/CosmWasm/cosmwasm-examples) repo.
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

```
git clone https://github.com/CosmWasm/cosmwasm-examples
cd cosmwasm-examples
git fetch
git checkout 44d6a256cd99e66849e550185c98671d4109d78b # current at time of writing, should be cw 1.0.0-beta
cd contracts/erc20
```

### COMPILE
We can compile our contract like so:
```
rustup default stable

cargo wasm
```
However, we want to create an optimised version to limit gas usage, so we're going to run:
```
sudo docker run --rm -v "$(pwd)":/code \
    --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
    --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
    cosmwasm/rust-optimizer:0.12.6
```
