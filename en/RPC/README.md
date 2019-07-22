# API Reference

Neo-CLI provides a set of API interfaces for obtaining blockchain data from a node to facilitate the development of blockchain applications. The interface is provided via [JSON-RPC](http://wiki.geekdream.com/Specification/json-rpc_2.0.html) and employs HTTP/HTTPS as the underlying protocol for communication. You can run the following command to start a node that provides the RPC service:

`dotnet neo-cli.dll /rpc`

## Configuring the config.json file

To access the RPC server via HTTPS, you need to set the domain name, certificate and password in the configuration file `config.json` before starting a node :

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

To invoke APIs related to the wallet component, you also need to make the following changes in `config.json` before starting a node:

- Change the attribute `IsActive` of the object `UnlockWallet` to `true`.
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

At this point, on the condition of the NEO-CLI launched, the client will automatically open the specified wallet and download the wallet index after the full block synchronization. 

## Listening ports 

After the JSON-RPC server starts, it will monitor the following ports corresponding to the MainNet and TestNet:

For P2P and WebSocket port information, please refer to [Node Introduction](../../../node/introduction.md).

|                | MainNet | TestNet |
| -------------- | -------- | -------- |
| JSON-RPC HTTPS | 10331    | 20331    |
| JSON-RPC HTTP  | 10332    | 20332    |

## Command List

| Command                                         | Parameter                                   | Description                                                  | Remark                     |
| ----------------------------------------------- | ------------------------------------------- | ------------------------------------------------------------ | ---------------------------- |
| [getbestblockhash](api/getbestblockhash.md)     |                                             | Get the hash of the latest block in the main chain         |                              |
| [getblock](api/getblock.md)                     | \<hash> [verbose=0]                         | Return the block information with the specified hash value |                              |
| [getblock](api/getblock2.md)                    | \<index> [verbose=0]                        | Return the block information with the specified index |                              |
| [getblockcount](api/getblockcount.md)           |                                             | Get the block count of the main chain                  |                              |
| [getblockhash](api/getblockhash.md)             | \<index>                                    | Return the block hash with the specified index |                              |
| [getblockheader](api/getblockheader.md)         | \<hash> [verbose=0]                         | Return the information of the block header with the specified script hash |                              |
| [getblockheader](api/getblockheader2.md)         | \<index> [verbose=0]                         | Return the information of the block header with the specified index |                              |
| [getblocksysfee](api/getblocksysfee.md)         | \<index>                                    | Return the system fees before the block with the specified index |                              |
| [getconnectioncount](api/getconnectioncount.md) |                                             | Get the current connection count of the node          |                              |
| [getcontractstate](api/getcontractstate.md)     | \<script_hash>                              | Return information of the contract with the specified script hash |                              |
| [getpeers](api/getpeers.md)                     |                                             | Get a list of nodes that are currently connected/disconnected by this node |                              |
| [getrawmempool](api/getrawmempool.md)           | [shouldGetUnverified=0]               | Get a list of unconfirmed transactions in memory            |                              |
| [getrawtransaction](api/getrawtransaction.md)   | \<txid> [verbose=0]                         | Return the transaction information with the specified hash value |                              |
| [getstorage](api/getstorage.md)                 | \<script_hash>  \<key>                      | Return the value with the contract script hash and the key |                              |
| [gettransactionheight](api/gettransactionheight.md)| \<txid>                                  | Return the block index in which the transaction is found. ||
| [getvalidators](api/getvalidators.md)           |                                             | Get the information about the validators                        |                              |
| [getversion](api/getversion.md)                 |                                             | Get the version information of the node                        |                              |
| [invokefunction](api/invokefunction.md)         | \<script_hash>  \<operation>  \<params>     | Invoke a smart contract with the specified script hash, passing in an operation and its params |                              |
| [invokescript](api/invokescript.md)             | \<script>                                   | Run a script through the virtual machine and returns the results |                              |
| [listplugins](api/listplugins.md)               |                                             | Return a list of plugins loaded by the node||
| [sendrawtransaction](api/sendrawtransaction.md) | \<hex>                                      | Broadcast a transaction over the network. |                              |
| [submitblock](api/submitblock.md)               | \<hex>                                      | Submit a new block to the network                             | Need to be a validator |
| [validateaddress](api/validateaddress.md)       | \<address>                                  | Verify whether the address is a valid NEO address             |                              |

## An example of GET request 

A typical format of JSON-RPC GET request is as follows:

The following is an example of querying the block count of the main chain.

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

## An example of POST request 

A typical format of JSON-RPC Post request is as follows:

The following is an example of querying the block count of the main chain.

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

> Note: If you choose the offline package to synchronize blocks, the program may not be able to respond to the API requests. It is recommended to synchronize blocks to the latest height before using the API, otherwise, the results returned may not be the latest.

## Test tools

You can use the Chrome extension `Postman` to facilitate the test (VPN required for installing the Chrome extension). The following is a test screenshot:

![](../../images/api_3.jpg)

## Changes in NEO3

1. NEO3 abandons the following commands: claimgas, dumpprivkey, getaccountstate, getapplicationlog, getassetstate, getbalance, getclaimable, getmetricblocktimestamp, getnep5balances, getnep5transfers, getnewaddress, gettxout, getunclaimed, getunclaimedgas, getunspents, getwalletheight, importprivkey, invoke, listaddress, sendfrom, sendtoaddress, sendmany, etc.
2. NEO3 redefines the following commands' references: getblockheader, getrawmempool.
3. NEO3 renews the following commands' returned content: getblock, getblockheader, getrawtransaction, getversion, getcontractstate.

## Others

[C# JSON-RPC Command List](https://github.com/chenzhitong/CSharp-JSON-RPC/blob/master/json_rpc/Program.cs)
