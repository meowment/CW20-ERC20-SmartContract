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
This will result in an artifact called cw_erc20.wasm being created in the artifacts directory.

### UPLOADING
```
cd artifacts
sourced tx wasm store cw_erc20.wasm  --from <your-key> --chain-id=<chain-id> \
  --gas-prices 0.1usource --gas auto --gas-adjustment 1.3 -b block -y
```

You will need to look in the output for this command for the code ID of the contract.
it will look like {"key":"code_id","value":"6"} in the output.

Or by doing these steps instead, and use the jq tool installed earlier to get the code_id value:
```
cd artifacts
TX=$(sourced tx wasm store cw_erc20.wasm  --from <your-key> --chain-id=<chain-id> --gas-prices 0.1usource --gas auto --gas-adjustment 1.3 -b block --output json -y | jq -r '.txhash')
CODE_ID=$(sourced query tx $TX --output json | jq -r '.logs[0].events[-1].attributes[0].value')
```
You can now see this value with:
```
echo $CODE_ID
```
### Initialize The Contract
```
> const initHash = {
  name: "Meow Coin",
  symbol: "MEOW",
  decimals: 6,
  initial_balances: [
    { address: "sourcevaloper1tr8h5v8t60kma86qemdg69zn4q38u8c8h8g0yd", amount: "12345678000"},
  ]
};
```
Then
```
> JSON.stringify(initHash);
```
```
sourced tx wasm instantiate 6 \
    '{"name":"Meow Coin","symbol":"MEOW","decimals":6,"initial_balances":[{"address":"<validator-self-delegate-address>","amount":"12345678000"}]}' \
    --amount 50000usource  --label "Meow Coin ERC20" --from <yourwallet> --chain-id <chainid> --gas-prices 0.1usource --gas auto --gas-adjustment 1.3 -b block -y
```
```
CONTRACT_ADDR=$(sourced query wasm list-contract-by-code $CODE_ID --output json | jq -r '.contracts[0]')
```
sourced query wasm contract $CONTRACT_ADDR
```
