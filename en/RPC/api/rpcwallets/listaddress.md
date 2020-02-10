# listaddress Method

Lists all the addresses in the current wallet.

> You should call the `openwallet` method to open the wallet first.



## Example

Request body：

```json
{
  "jsonrpc": "2.0",
  "method": "listaddress",
  "params": [],
  "id": 1
}
```

Response body：

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        {
            "address": "ASL3KCvJasA7QzpYGePp25pWuQCj4dd9Sy",
            "haskey": true,
            "label": null,
            "watchonly": false
        },
        {
            "address": "AV2Ai7PXcNbjTSeKgWqsDEjLaEAJZpytru",
            "haskey": true,
            "label": null,
            "watchonly": false
        }
    ]
}
```

Response description：

address：The address in the wallet.

watchonly： Indicates whether it is a watch only address.