# API Reference

Each node in the Neo-CLI provides an API interface for obtaining blockchain data from a node, making it easy to develop blockchain applications. The interface is provided via [JSON-RPC](http://wiki.geekdream.com/Specification/json-rpc_2.0.html), and the underlying protocol uses HTTP/HTTPS for communication. To start a node that provides an RPC service, run the following command:

`dotnet neo-cli.dll /rpc`

## Configuring the config.json file

To access the RPC server via HTTPS, you need to modify the configuration file config.json before starting the node and set the domain name, certificate, and password:

```json
{
  "ApplicationConfiguration": {
    "Paths": {
      "Chain": "Chain"
    },
    "P2P": {
      "Port": 10333,
      "WsPort": 10334
    },
    "RPC": {
      "Port": 10331,
      "SslCert": "YourSslCertFile.xxx",
      "SslCertPassword": "YourPassword"
    }
  }
}                                      
```

To invoke some API methods that require you to open a wallet, you also need to make the following changes in `config.json` before starting the node:

- Change the UnlockWallet status `IsActive` to `true`.
- Specify the file name and password of the desired wallet.

```json
...
"UnlockWallet": {
      "Path": "YourWallet.json",
      "Password": "YourPassword",
      "StartConsensus": false,
      "IsActive": true
    }
...
```

Thereafter, when you open NEO-CLI, the client will automatically open the specified wallet and download the wallet index after it has been synchronized to the latest block height. 

## Listening ports 

After the JSON-RPC server starts, it will monitor the following ports, corresponding to the Main and Test nets:

For P2P and WebSocket information see [Node Introduction](../../../node/introduction.md).

|                | Main Net | Test Net |
| -------------- | -------- | -------- |
| JSON-RPC HTTPS | 10331    | 20331    |
| JSON-RPC HTTP  | 10332    | 20332    |

## Command List

| Command                                         | Reference                                   | Explanation                                                  | Comments                     |
| ----------------------------------------------- | ------------------------------------------- | ------------------------------------------------------------ | ---------------------------- |
| [getbestblockhash](api/getbestblockhash.md)     |                                             | Gets the hash of the tallest block in the main chain         |                              |
| [getblock](api/getblock.md)                     | \<hash> [verbose=0]                         | Returns the corresponding block information according to the specified hash value |                              |
| [getblock](api/getblock2.md)                    | \<index> [verbose=0]                        | Returns the corresponding block information according to the specified index |                              |
| [getblockcount](api/getblockcount.md)           |                                             | Gets the number of blocks in the main chain                  |                              |
| [getblockhash](api/getblockhash.md)             | \<index>                                    | Returns the hash value of the corresponding block based on the specified index |                              |
| [getblockheader](api/getblockheader.md)         | \<hash> [verbose=0]                         | Returns the corresponding block header information according to the specified script hash |                              |
| [getblockheader](api/getblockheader2.md)         | \<index> [verbose=0]                         | Returns the corresponding block header information according to the specified index |                              |
| [getblocksysfee](api/getblocksysfee.md)         | \<index>                                    | Returns the system fees before the block according to the specified index |                              |
| [getconnectioncount](api/getconnectioncount.md) |                                             | Gets the current number of connections for the node          |                              |
| [getcontractstate](api/getcontractstate.md)     | \<script_hash>                              | Returns information about the contract based on the specified script hash |                              |
| [getpeers](api/getpeers.md)                     |                                             | Gets a list of nodes that are currently connected/disconnected by this node |                              |
| [getrawmempool](api/getrawmempool.md)           | [shouldGetUnverified=0]               | Gets a list of unconfirmed transactions in memory            |                              |
| [getrawtransaction](api/getrawtransaction.md)   | \<txid> [verbose=0]                         | Returns the corresponding transaction information based on the specified hash value |                              |
| [getstorage](api/getstorage.md)                 | \<script_hash>  \<key>                      | Returns the stored value based on the contract script hash and key |                              |
| [gettransactionheight](api/gettransactionheight.md)| \<txid>                                  | Returns the block index in which the transaction is found. ||
| [getvalidators](api/getvalidators.md)           |                                             | Gets NEO consensus nodes information                         |                              |
| [getversion](api/getversion.md)                 |                                             | Gets version information of this node                        |                              |
| [invokefunction](api/invokefunction.md)         | \<script_hash>  \<operation>  \<params>     | Invokes a smart contract at specified script hash, passing in an operation and its params |                              |
| [invokescript](api/invokescript.md)             | \<script>                                   | Runs a script through the virtual machine and returns the results |                              |
| [listplugins](api/listplugins.md)               |                                             | Returns a list of plugins loaded by the node.||
| [sendrawtransaction](api/sendrawtransaction.md) | \<hex>                                      | Broadcast a transaction over the network. |                              |
| [submitblock](api/submitblock.md)               | \<hex>                                      | Relay a new block to the network                             | Needs to be a consensus node |
| [validateaddress](api/validateaddress.md)       | \<address>                                  | Verify that the address is a correct NEO address             |                              |

## GET request example

A typical JSON-RPC GET request format is as follows:

The following is an example of how to get the number of blocks in the main chain.

Request URL:

```
http://somewebsite.com:10332?jsonrpc=2.0&method=getblockcount&params=[]&id=1
```

After sending the request, you will get the following response:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": 909129
}
```

## POST request example

The format of a typical JSON-RPC Post request is as follows:

The following is an example of how to get the number of blocks in the main chain.

Request URL:

```
http://somewebsite.com:10332
```

Request Body：

```json
{
  "jsonrpc": "2.0",
  "method": "getblockcount",
  "params":[],
  "id": 1
}
```

After sending the request, you will get the following response：

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": 909122
}
```

## Test tools

You can use the Chrome extension in Postman to facilitate the test (Installation of the Chrome extension requires Internet connection), the following is a test screenshot:

![](../../images/api_3.jpg)

## Other

[C# JSON-RPC Command List](https://github.com/chenzhitong/CSharp-JSON-RPC/blob/master/json_rpc/Program.cs)
