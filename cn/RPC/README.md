﻿# RPC
<!-- TOC -->

- [RPC](#rpc)
    - [NEO3变更部分](#neo3变更部分)
    - [API 参考](#api-参考)
        - [修改配置文件](#修改配置文件)
        - [监听端口](#监听端口)
        - [命令列表](#命令列表)
        - [RpcServer.Wallet相关方法](#rpcserverwallet相关方法)
        - [GET 请求示例](#get-请求示例)
        - [POST 请求示例](#post-请求示例)
    - [测试工具](#测试工具)
    - [其它](#其它)

<!-- /TOC -->

## NEO3变更部分

- 更新
    - 调用方式：[getblockheader](api/getblockheader.md)，[getrawmempool](api/getrawmempool.md)
    - 返回结果：[getblock](api/getblock.md)，[getblockheader](api/getblockheader.md)，[getrawtransaction](api/getrawtransaction.md)，[getversion](api/getversion.md)，[getcontractstate](api/getcontractstate.md)

- 删除
    - `claimgas`,  `getaccountstate`, `getassetstate`, `getclaimable`, `getmetricblocktimestamp`, `gettxout`, `getunspents`, `invoke`


## API 参考

每个 Neo 节点 Neo-CLI 都可选的提供了一套 API 接口，用于从节点获取区块链数据，使得开发区块链应用变得十分方便。接口通过 [JSON-RPC](http://wiki.geekdream.com/Specification/json-rpc_2.0.html) 的方式提供，底层使用 HTTP/HTTPS 协议进行通讯。要启动一个提供 RPC 服务的节点，需要使用以下命令安装RpcServer插件：

`install RpcServer`

### 修改配置文件

* **HTTPS 配置**：若要通过 HTTPS 的方式访问 RPC 服务器，需要在安装插件前修改`RpcServer`插件的配置文件 `config.json`，并设置域名、证书和密码：

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

* **开启钱包:** 如果需要通过RPC调用钱包相关的服务，首先需要调用`openwallet`方法打开钱包.

  完成配置后重启 NEO-CLI，则可开始使用RPC服务。

### 监听端口

JSON-RPC 服务器启动后，会监听 TCP 端口，默认端口如下。P2P 和 WebSocket 的端口详见 [Neo 节点介绍](https://docs.neo.org/docs/zh-cn/node/introduction.html)。

|                | 主网（Main Net） | 测试网（Test Net） |
| -------------- | ------------ | ------------- |
| JSON-RPC HTTPS | 10331        | 20331         |
| JSON-RPC HTTP  | 10332        | 20332         |

### 命令列表
**NEO3 变更**：

>调用方式更新：getblockheader，getrawmempool
>
>返回结果更新：getblock，getblockheader，getrawtransaction，getversion，getcontractstate

| 方法           | 参数      | 说明        | 备注       |
| ----------- | ---------- | ------------- | -------- |
| [getbestblockhash](api/getbestblockhash.md) |                                          | 获取主链中高度最大的区块的散列              |          |
| [getblock](api/getblock.md)              | \<hash> [verbose=0]                      | 根据指定的散列值，返回对应的区块信息           |          |
|[getblock](api/getblock2.md) | \<index> [verbose=0]                     | 根据指定的索引，返回对应的区块信息            |          |
| [getblockcount](api/getblockcount.md)    |                                          | 获取主链中区块的数量                   |          |
| [getblockhash](api/getblockhash.md)      | \<index>                                 | 根据指定的索引，返回对应区块的散列值           |          |
| [getblockheader](api/getblockheader.md) | \<hash> [verbose=0] | 根据指定的散列值，返回对应的区块头信息。 | |
| [getblockheader](api/getblockheader2.md)| \<index> [verbose=0] | 根据指定的索引，返回对应的区块头信息。 | |
| [getblocksysfee](api/getblocksysfee.md)  | \<index>                                 | 根据指定的索引，返回截止到该区块前的系统手续费      |          |
| [getconnectioncount](api/getconnectioncount.md) |                                          | 获取节点当前的连接数                   |          |
| [getcontractstate](api/getcontractstate.md) | \<script_hash>                           | 根据合约脚本散列，查询合约信息              |          |
| [getpeers](api/getpeers.md)              |                                          | 获得该节点当前已连接/未连接的节点列表          |          |
| [getrawmempool](api/getrawmempool.md)    | [shouldGetUnverified=0]         | 获取内存中未确认的交易列表                |          |
| [getrawtransaction](api/getrawtransaction.md) | \<txid> [verbose=0]                      | 根据指定的散列值，返回对应的交易信息           |          |
| [getstorage](api/getstorage.md)          | \<script_hash>  \<key>                   | 根据合约脚本散列和存储的 key，返回存储的 value |          |
| [gettransactionheight](api/gettransactionheight.md) | \<txid> | 获取交易高度。 | |
| [getvalidators](api/getvalidators.md) | | 查看当前共识节点的信息 | |
| [getversion](api/getversion.md)          |                                          | 获取查询节点的版本信息                  |          |
| [invokefunction](api/invokefunction.md)  | \<script_hash>  \<operation>  \<params>  | 以指定的脚本散列值调用智能合约，传入操作及参数      |          |
| [invokescript](api/invokescript.md)      | \<script>                                | 通过虚拟机运行脚本并返回结果               |          |
| [listplugins](api/listplugins.md) | | 列出节点已加载的所有插件。                           |  |
| [sendrawtransaction](api/sendrawtransaction.md) | \<hex>                                   | 广播交易                         |          |
| [submitblock](api/submitblock.md) | \<hex>                                   | 提交新的区块                       | 需要成为共识节点 |
| [validateaddress](api/validateaddress.md) | \<address>                               | 验证地址是否是正确的 Neo 地址            |          |
| [getapplicationlog](api/getapplicationlog.md) | \<txid>                               | 根据指定的交易ID 获取合约日志            |          |  
| [getnep5transfers](api/getnep5transfers.md) | \<address> \<timestamp>                               | 返回指定地址内的所有 NEP-5 交易记录            |          | 
| [getnep5balances](api/getnep5balances.md) | \<address>                               | 返回指定地址内的所有 NEP-5 资产余额            |          | 

### RpcServer.Wallet相关方法

| 方法           | 参数      | 说明        | 备注       |
| ----------- | ---------- | ------------- | -------- |
| [closewallet](api/rpcwallets/closewallet.md) | | 关闭已打开的钱包 |  |
| [openwallet](api/rpcwallets/openwallet.md) | \<path>\<password> | 打开指定路径下的钱包 |  |
| [dumpprivkey ](api/rpcwallets/dumpprivkey.md) | \<address> | 导出指定地址的私钥 |  |
| [getbalance](api/rpcwallets/getbalance.md) | \<asset_id> | 查询资产余额 | |
| [getnewaddress](api/rpcwallets/getnewaddress.md) |  | 创建一个新的地址 | |
| [getunclaimedgas](api/rpcwallets/getunclaimedgas.md) |  | 显示钱包中未提取的 GAS 数量 | |
| [importprivkey](api/rpcwallets/importprivkey.md) | \<key> | 导入私钥到钱包 | |
| [listaddress](api/rpcwallets/listaddress.md) |  | 列出当前钱包内的所有地址 | |
| [sendfrom](api/rpcwallets/sendfrom.md) | \<asset_id>\<from>\<to>\<value> | 从指定地址，向指定地址转账 | |
| [sendmany](api/rpcwallets/sendmany.md) | \<outputs_array> | 在一笔交易中向指定地址发起多笔转账 | |
| [sendtoaddress](api/rpcwallets/sendtoaddress.md) | \<asset_id>\<address>\<value> | 向指定地址转账 | |




### GET 请求示例

一次典型的 JSON-RPC GET 请求格式如下：

下面以获取主链中区块的数量方法为例。

请求 URL：

```
http://somewebsite.com:10332?jsonrpc=2.0&method=getblockcount&params=[]&id=1
```

发送请求后，将会得到如下的响应：

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": 909129
}
```

### POST 请求示例

一次典型的 JSON-RPC Post 请求的格式如下：

下面以获取主链中区块的数量方法为例。

请求 URL：

```
http://somewebsite.com:10332
```

请求 Body：

```json
{
  "jsonrpc": "2.0",
  "method": "getblockcount",
  "params": [],
  "id": 1
}
```

发送请求后，将会得到如下的响应：

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": 909122
}
```

> 注：当使用离线同步包同步区块时，程序可能无法响应 API 请求，建议将区块同步到最新高度后再使用 API，否则返回的结果可能不是最新的。

## 测试工具

你可以用 Chrome 扩展程序中的 Postman 来方便地进行测试（安装 Chrome 扩展程序需要科学上网），下面是测试截图：

![](../../images/api_3.jpg)


## 其它

[C# JSON-RPC 使用方法](https://github.com/chenzhitong/CSharp-JSON-RPC/blob/master/json_rpc/Program.cs)

*点击[此处](../../en/RPC)查看RPC英文版*
