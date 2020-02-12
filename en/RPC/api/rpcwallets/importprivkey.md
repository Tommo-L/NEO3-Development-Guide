# importprivkey Method

Imports the private key to the wallet.

> 1. Install the plugin [RpcServer](https://github.com/neo-project/neo-plugins/releases) 
> 2. Call the RPC method `openwallet` to open the wallet first.



```json
{
  "jsonrpc": "2.0",
  "method": "importprivkey",
  "params": [key],
  "id": 1
}
```



### Parameter Description

* key: The WIF-format private key.



## Example

Request body:

```json
{
  "jsonrpc": "2.0",
  "method": "importprivkey",
  "params": ["L5c6jz6Rh8arFJW3A5vg7Suaggo28ApXVF2EPzkAXbm94ThqaA6r"],
  "id": 1
}
```

Response body:

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "address": "Ad8S24trcuchyLfEbJWqRP7BUScUT4t2pw",
        "haskey": true,
        "label": null,
        "watchonly": false
    }
}
```

Response description:

Returns the address corresponding to the key.