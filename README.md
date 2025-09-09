# Nyks TX Decoder

This service is a JSON-RPC server that decodes a base64 encoded Cosmos transaction, extracts the sender's public key and address, and checks if the address is whitelisted by querying a faucet service.

## RPC Method

The server exposes the following JSON-RPC method:

### `broadcast_tx_sync`

This method accepts a single parameter: a base64 encoded transaction string.

#### Parameters

1.  `tx`: `String` - The base64 encoded transaction.

#### Returns

A JSON object containing the derived address and its verification status.

- `address`: `String` - The Bech32 encoded address with the prefix `twilight`.
- `verified`: `bool` - A boolean indicating whether the address is whitelisted.

## Client Interaction

You can interact with the RPC server using any JSON-RPC client. Here is an example using `curl`.

### `curl` Example

To call the `broadcast_tx_sync` method, you need to send a JSON-RPC request to the server. The `params` field can be an array with the transaction string, or an object with a `tx` field.

```bash
curl -X POST -H "Content-Type: application/json" --data '{
    "jsonrpc": "2.0",
    "method": "broadcast_tx_sync",
    "params": {
        "tx": "YOUR_BASE64_ENCODED_TX_HERE"
    },
    "id": 1
}' https://rpc.testnet.twilight.rest | jq .
```

Replace `YOUR_BASE64_ENCODED_TX_HERE` with your actual base64 encoded transaction string. The output is piped to `jq` for pretty-printing.

### Example Response

If the request is successful, the server will return a JSON object like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "address": "twilight1f02k7y0n6g4T9s2q0e8j7r6c3m5a4d9e8f7g6",
    "verified": true
  },
  "id": 1
}
```

### Response Fields Explained

- `jsonrpc`: The JSON-RPC version.
- `result`: The result object.
  - `address`: The bech32 encoded address of the transaction sender.
  - `verified`: `true` if the address is whitelisted, `false` otherwise.
- `id`: The ID of the request.

## Environment Setup

This service uses the following environment variables:

- `FAUCET_HOST`: The URL of the faucet service to check for whitelisting. Defaults to `http://172.17.0.1:6969`.
- `WHITELIST_RPC_URL`: The address and port for the RPC server to listen on. Defaults to `0.0.0.0:8080`.

You can create a `.env` file in the root of the project to set these variables.

```
FAUCET_HOST=http://172.17.0.1:6969
WHITELIST_RPC_URL=0.0.0.0:8080
```

## Docker

You can build and run this service using Docker.

### Build the Docker image

```bash
docker build -t nyks_tx_decoder .
```

### Run the Docker container

```bash
docker run --rm -p 8080:8080 --env-file .env nyks_tx_decoder
```

This command will run the container and forward the port `8080` from the container to `8080` on the host. It also uses the `.env` file for configuration.
