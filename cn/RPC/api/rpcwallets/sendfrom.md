﻿# sendfrom 方法

从指定地址，向指定地址转账。

> - 执行此命令前需要 RPC 调用 openwallet 方法来打开钱包
> - 此方法由插件提供，需要安装 [RpcServer](https://github.com/neo-project/neo-modules/releases) 插件才可以调用



```json
{
  "jsonrpc": "2.0",
  "method": "sendfrom",
  "params": [asset_id, from, to, value],
  "id": 1
}
```



### 参数说明

* asset_id：资产 ID（资产标识符），即NEP-5合约的scripthash
* from：转账地址。
* to: 收款地址。
* value：转账金额。



## 调用示例

请求正文：

```json
{
  "jsonrpc": "2.0",
  "method": "sendfrom",
  "params": ["602c79718b16e442de58778e148d0b1084e3b2dffd5de6b7b16cee7969282de7","AWg3L6W68bFfSS13Tf4rt8CRdG2ktaAjGb","AWg3L6W68bFfSS13Tf4rt8CRdG2ktaAjGb",1],
  "id": 1
}
```

响应正文：

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "txid": "0x60170ad03627ce45c7dd56ececbf33b26eab0845aa8b2cbbeecaefc5771b9eb1",
    "size": 262,
    "type": "ContractTransaction",
    "version": 0,
    "attributes": [],
    "vin": [
      {
        "txid": "0xd2188c1bd454ac883d79826e5c677deedb91cc61ec6d819df48ff4a963873adb",
        "vout": 1
      }
    ],
    "vout": [
      {
        "n": 0,
        "asset": "0x602c79718b16e442de58778e148d0b1084e3b2dffd5de6b7b16cee7969282de7",
        "value": "1",
        "address": "AWg3L6W68bFfSS13Tf4rt8CRdG2ktaAjGb"
      },
      {
        "n": 1,
        "asset": "0x602c79718b16e442de58778e148d0b1084e3b2dffd5de6b7b16cee7969282de7",
        "value": "17.4798197",
        "address": "AWg3L6W68bFfSS13Tf4rt8CRdG2ktaAjGb"
      }
    ],
    "sys_fee": "0",
    "net_fee": "0",
    "scripts": [
      {
        "invocation": "40a8d40e1652d7ad0c7bb59ef8217237037824af54ee5e46f2fd096c44dd46ef27fa7255010e2a8a2166af8a904e13b96bd3ac82e791633685824c35e7f2731e79",
        "verification": "2102883118351f8f47107c83ab634dc7e4ffe29d274e7d3dcf70159c8935ff769bebac"
      }
    ]
  }
}
```

响应说明：

返回如上的交易详情说明交易发送成功，否则交易发送失败。

如果签名不完整会返回待签名的交易。

如果余额不足会返回错误信息。
