# Cosmwasm SendReceive Smart Contract
This project contains the Cosmwasm smart contract that can send and receive message payloads to/from EVM.

This contract is deployed to uni-6 testnet: `juno1h8fnj9qrjyecx7zhwtgvpucxpvztpa7gz28jztxxz8509uvn0fasa4gmzx`

# Tests
Unit tests can be run with `cargo test`

# Build and deploy
This process assumes you're using junod CLI: https://docs.junonetwork.io/

1. Build
```
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer:0.12.13
  ```

2. Deploy and store the <code_id>
```
junod tx wasm store ./artifacts/send_receive.wasm --from wallet --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.5 -y -b block
```


3. Instantiate with <code_id>, store the <contract_address>
```
junod tx wasm instantiate <code_id> '{}' --from wallet --label "send_receive" --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 --no-admin -y -b block
```

4. Example send message from Osmosis -> Avalache Fuji
```
junod tx wasm execute <contract_address> '{"send_message_evm": {"destination_chain": "Avalanche", "destination_address":"<destination_address>","message":"hello"}}' --amount 1ujunox --from wallet --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -y -b block
```

5. Example send message transaction from Juno -> Juno via Axelar
```
junod tx wasm execute <contract_address> '{"send_message_cosmos": {"destination_chain": "uni-6", "destination_address":"<destination_address>","message":"hello"}}' --amount 1ujunox --from wallet --gas-prices 0.1ujunox --gas auto --gas-adjustment 1.3 -y -b block
```

6. Example query stored message
```
junod query wasm contract-state smart <contract_address> '{"get_stored_message":{}}'
```