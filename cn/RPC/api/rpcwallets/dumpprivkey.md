﻿# dumpprivkey 方法

导出指定地址的私钥。

> - 执行此命令前需要 RPC 调用 openwallet 方法来打开钱包
> - 此方法由插件提供，需要安装 [RpcServer](https://github.com/neo-project/neo-modules/releases) 插件才可以调用

```json
{
  "jsonrpc": "2.0",
  "method": "dumpprivkey",
  "params": [address]
  "id": 1
}
```



### 参数说明

* address：要导出私钥的地址，该地址需为标准地址。



## 调用示例

请求正文：s

```json
{
  "jsonrpc": "2.0",
  "method": "dumpprivkey",
  "params": ["ASMGHQPzZqxFB2yKmzvfv82jtKVnjhp1ES"],
  "id": 1
}
```

响应正文：

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "L3FdgAisCmV******************************9XM65cvjYQ1"
}
```

响应说明：

返回该标准地址的私钥。