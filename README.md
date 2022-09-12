# CW20-ERC20-Tutorial

This will take you through uploading your own CW-20/ERC-20 to the Source Chain testnet.
It uses the examples in the contracts/erc20 folder of the [cosmwasm-examples](https://github.com/CosmWasm/cosmwasm-examples) repo.
There are four steps involved in working with a smart contract.

- Write the smart contract (already done here!)
- Store the smart contract on chain
- Instantiate the smart contract (configure and initialise it)
- Execute commands provided by the smart contract

We will go through all of these in this tutorial.


### INSTALLING

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
```
apt-get install cargo.io
```
```
apt-get install nodejs
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
sourced tx wasm store cw_erc20.wasm  --from <yourwallet> --chain-id=<chain-id> \
  --gas-prices 0.1usource --gas auto --gas-adjustment 1.3 -b block -y
```

You will need to look in the output for this command for the code ID of the contract.
it will look like {"key":"code_id","value":"6"} in the output.

Add Variables
```
CODE_ID=<yourcodeid>
```
You can now see this value with:
```
echo $CODE_ID
```
### Initialize The Contract
This example using nodejs, type node and enter to access it
```
const initHash = {
  name: "Meow Coin",
  symbol: "MEOW",
  decimals: 6,
  initial_balances: [
    { address: "<yourwalletaddress>", amount: "12345678000"},
  ]
};
```
Then
```
JSON.stringify(initHash);
```
```
sourced tx wasm instantiate $CODE_ID \
    '{"name":"Meow Coin","symbol":"MEOW","decimals":6,"initial_balances":[{"address":"<yourwalletaddress>","amount":"12345678000"}]}' \
    --amount 50000usource  --label "Meow Coin ERC20" --from <yourwallet> --chain-id <chainid> --admin <yourwallet> --gas-prices 0.1usource --gas auto --gas-adjustment 1.3 -b block -y
```
if success you will get output
```
CONTRACT_ADDR=$(sourced query wasm list-contract-by-code $CODE_ID --output json | jq -r '.contracts[0]')
```
```
sourced query wasm contract $CONTRACT_ADDR
```
Or check contract address list with:
```
sourced query wasm list-contract-by-code $CODE_ID
```

### Query And Run Commands
Check that the contract has assigned the right amount balance
```
sourced query wasm contract-state smart <contract-address> '{"balance":{"address":"<yourwalletaddress>"}}'
```
From the example above, it will return:
```
data:
  balance: "12345678000"
```

Excute command
```
sourced tx wasm execute <contract-addr> '{"transfer":{"amount":"200","owner":"<yourwalletaddress>","recipient":"<recipient-address>"}}' --amount 1000usource --from <yourwallet> --chain-id <chainid> --gas-prices 0.1usource --gas auto --gas-adjustment 1.3 -b block -y
```
