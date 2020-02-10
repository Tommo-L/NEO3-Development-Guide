# getnewaddress Method

Creates a new address.

> You should call the `openwallet` method to open the wallet first.



## Example

Request body：

```json
{
  "jsonrpc": "2.0",
  "method": "getnewaddress",
  "params": [],
  "id": 1
}
```

Response body：

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "AVHcdW3FGKbPWGHNhkPjgVgi4GGndiCxdo"
}
```

Response Description:

Returns the newly created address.