﻿# sendtoaddress Method

Transfers to the specified address.

> 1. Install the plugin [RpcServer](https://github.com/neo-project/neo-plugins/releases) 
> 2. Call the RPC method `openwallet` to open the wallet first.



```json
{
  "jsonrpc": "2.0",
  "method": "sendtoaddress",
  "params": [asset_id,address,value],
  "id": 1
}
```



### Parameter Description

* asset_id： Asset ID (asset identifier),  the script hash of NEP-5 contract
* address：Payment address
* value：Amount transferred



## Example

Request body:

```json
{
  "jsonrpc": "2.0",
  "method": "sendtoaddress",
  "params": ["c56f33fc6ecfcd0c225c4ab356fee59390af8560be0e930faebe74a6daff7c9b","AK4if54jXjSiJBs6jkfZjxAastauJtjjse",1],
  "id": 1
}
```

Response body:

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "txid": "0x06de043b9b914f04633c580ab02d89ba55556f775118a292adb6803208857c91",
        "size": 262,
        "type": "ContractTransaction",
        "version": 0,
        "attributes": [],
        "vin": [
            {
                "txid": "0x9c20c13f6b05691efbfd7e420b0edf470f8a5ae467e1e7ca7e11243c9b9fc333",
                "vout": 2
            }
        ],
        "vout": [
            {
                "n": 0,
                "asset": "0xc56f33fc6ecfcd0c225c4ab356fee59390af8560be0e930faebe74a6daff7c9b",
                "value": "1",
                "address": "AK4if54jXjSiJBs6jkfZjxAastauJtjjse"
            },
            {
                "n": 1,
                "asset": "0xc56f33fc6ecfcd0c225c4ab356fee59390af8560be0e930faebe74a6daff7c9b",
                "value": "497",
                "address": "AK5q8peiC4QKwuZHWX5Dkqhmar1TAGvZBS"
            }
        ],
        "sys_fee": "0",
        "net_fee": "0",
        "scripts": [
            {
                "invocation": "4059e40a2040fe43bf8a40230e1f136dcfe7b3ca37d492ac8d6439615f7b88601c8d9b8077cd0e4f8c9f402d10a2782945bfa50e0ed3f57f7cceebd2f792453eb0",
                "verification": "2103cf5ba6a9135f8eaeda771658564a855c1328af6b6808635496a4f51e3d29ac3eac"
            }
        ]
    }
}
```

Response Description:

Returning of the transaction details above, indicates that the transaction was sent successfully. If not, the transaction has failed to send.

If the signature is incomplete, it will return the transaction to be signed.

If the balance is insufficient, it will return an error message.
