# getunclaimedgas Method

Gets the amount of unclaimed GAS in the wallet.

> You should call the `openwallet` method to open the wallet first.



## Example

Request body:

```json
{
  "jsonrpc": "2.0",
  "method": "getunclaimedgas",
  "params": [],
  "id": 1
}
```

Response body:

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "available": "0.140771",
        "unavailable": "0.096224"
    }
}
```

Response description:

Returns the unclaimed GAS amount.