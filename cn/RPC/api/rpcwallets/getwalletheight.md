# getwalletheight 方法

获取当前钱包索引高度

> 执行此命令前需要先调用`openwallet`方法打开钱包。



## 调用示例

请求正文：

```json
{
  "jsonrpc": "2.0",
  "method": "getwalletheight",
  "params": [],
  "id": 1
}
```

响应正文：

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": 2713183
}
```

