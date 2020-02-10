# openwallet 

Open the wallet in the specified path.

```json
{
  "jsonrpc": "2.0",
  "method": "openwallet",
  "params": [path, password]
  "id": 1
}
```

### Parameter Description

* path：path of the wallet to be opened
* password：password of the wallet

## Example

Request Body：

```json
{
  "jsonrpc": "2.0",
  "method": "openwallet",
  "params": ["1.json", "1111"],
  "id": 1
}
```

Response Body：

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": true
}
```

Response Description：

Return whether the wallet is opened successfully or not.