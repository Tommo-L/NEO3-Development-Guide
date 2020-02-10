
# RPC
<!-- TOC -->

- [RPC](#rpc)
    - [Changes in NEO3](#changes-in-neo3)
    - [API Reference](#api-reference)
        - [Setting configuration](#setting-configuration)
        - [Listening ports](#listening-ports)
        - [Command list](#command-list)
        - [RpcServer.Wallet Methods](#rpcserverwallet-methods)
        - [An example of GET request](#an-example-of-get-request)
        - [An example of POST request](#an-example-of-post-request)
    - [Test tools](#test-tools)
    - [Others](#others)

<!-- /TOC -->

## Changes in NEO3

- UPDATE
    - Invocation Style: [getblockheader](api/getblockheader.md)，[getrawmempool](api/getrawmempool.md)
    - Returns: [getblock](api/getblock.md)，[getblockheader](api/getblockheader.md)，[getrawtransaction](api/getrawtransaction.md)，[getversion](api/getversion.md)，[getcontractstate](api/getcontractstate.md)

- DELETE
    - Discard the following commands: `claimgas`,  `getaccountstate`, `getassetstate`, `getclaimable`, `getmetricblocktimestamp`, `gettxout`, `getunspents`, `invoke`

## API Reference

Neo-CLI provides a set of API interfaces for obtaining blockchain data from a node to facilitate the development of blockchain applications. The interface is provided via [JSON-RPC](http://wiki.geekdream.com/Specification/json-rpc_2.0.html) and employs HTTP/HTTPS as the underlying protocol for communication. You can run the following command to install the RpcServer plugin:

`install RpcServer`

### Setting configuration

- **HTTPS**: To access the RPC server via HTTPS, you need to set the domain name, certificate and password in the configuration file `config.json` of the `RpcServer` before installing the plugin:

  ```json
  {
  "PluginConfiguration": {
    "BindAddress": "127.0.0.1",
    "Port": 10332,
    "SslCert": "YourSslCertFile.xxx",
    "SslCertPassword": "YourPassword",
    "TrustedAuthorities": [],
    "RpcUser": "",
    "RpcPass": "",
    "MaxGasInvoke": 10,
    "MaxFee": 0.1,
    "MaxConcurrentConnections": 40,
    "DisabledMethods": []
  }                                 
  ```

- **Open the wallet**: If you want to invoke wallet-related methods through RPC service, you should first call the `openwallet` method to open the wallet:

At this point, you can restart the neo-cli to enable the RPC service.

### Listening ports

After the JSON-RPC server starts, it will monitor the following ports corresponding to the MainNet and TestNet:

For P2P and WebSocket port information, please refer to [Node Introduction](../../../node/introduction.md).

|                | MainNet | TestNet |
| -------------- | -------- | -------- |
| JSON-RPC HTTPS | 10331    | 20331    |
| JSON-RPC HTTP  | 10332    | 20332    |

### Command list

| Command                                         | Parameter                                   | Description                                                  | Remark                     |
| ----------------------------------------------- | ------------------------------------------- | ------------------------------------------------------------ | ---------------------------- |
| [getbestblockhash](api/getbestblockhash.md)     |                                             | Get the hash of the latest block in the main chain         |                              |
| [getblock](api/getblock.md)                     | \<hash> [verbose=0]                         | Return the block information with the specified hash value |                              |
|  [getblock](api/getblock2.md)                                               | \<index> [verbose=0]                        | Return the block information with the specified index |                              |
| [getblockcount](api/getblockcount.md)           |                                             | Get the block count of the main chain                  |                              |
| [getblockhash](api/getblockhash.md)             | \<index>                                    | Return the block hash with the specified index |                              |
| [getblockheader](api/getblockheader.md)         | \<hash> [verbose=0]                         | Return the information of the block header with the specified script hash |                              |
|  [getblockheader](api/getblockheader2.md)                                               | \<index> [verbose=0]                         | Return the information of the block header with the specified index |                              |
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
| [getapplicationlog](api/getapplicationlog.md) | \<txid>                               | Return the contract log based on the specified txid            |          |  
| [getnep5transfers](api/getnep5transfers.md) | \<address> \<timestamp>                               | Return all the NEP-5 transaction information occurred in the specified address            |          | 
| [getnep5balances](api/getnep5balances.md) | \<address>                               | Return the balance of all NEP-5 assets in the specified address           |          | 


### RpcServer.Wallet Methods

| Command                                               | Parameter                                              | Description                                                  | Remark |
| ----------------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------ | ------ |
| closewallet       |                                             | Close the wallet            |        |
| [openwallet](api/rpcwallets/openwallet.md)         | \<address>                                             | Open the wallet in the specified path             |        |
| [dumpprivkey ](api/rpcwallets/dumpprivkey.md)         | \<address>                                             | Exports the private key of the specified address             |        |
| [getbalance](api/rpccwallets/getbalance.md)           | \<asset_id>                                            | Returns the balance of the corresponding asset in the wallet |        |
| [getnewaddress](/api/rpcwallets/getnewaddress.md)     |                                                        | Creates a new address                                        |        |
| [getunclaimedgas](/api/rpcwallets/getunclaimedgas.md) |                                                        | Gets the amount of unclaimed GAS in the wallet               |        |
| [importprivkey](/api/rpcwallets/importprivkey.md)     | \<key>                                                 | Imports the private key to the wallet                        |        |
| [listaddress](/api/rpcwallets/listaddress.md)         |                                                        | Lists all the addresses in the current wallet                |        |
| [sendfrom](/api/rpcwallets/sendfrom.md)               | \<asset_id>\<from>\<to>\<value> | Transfer from the specified address to the destination address |        |
| [sendmany](/api/rpcwallets/sendmany.md)               | \<outputs_array>              | Initiate multiple transfers to designated addresses in a transaction   |        |
| [sendtoaddress](/api/rpcwallets/sendtoaddress.md)     | \<asset_id>\<address>\<value>  | Transfers to the specified address.                          |        |



### An example of GET request

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

### An example of POST request

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

## Others

[C# JSON-RPC Command List](https://github.com/chenzhitong/CSharp-JSON-RPC/blob/master/json_rpc/Program.cs)

*Click [here](../../cn/RPC) to see the Chinese edition of the RPC*
