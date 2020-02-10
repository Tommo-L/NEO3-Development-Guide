# openwallet 方法

打开指定路径下的钱包。

```json
{
  "jsonrpc": "2.0",
  "method": "openwallet",
  "params": [path, password]
  "id": 1
}
```

### 参数说明

* path：要打开的钱包文件路径
* password：钱包密码

## 调用示例

请求正文：

```json
{
  "jsonrpc": "2.0",
  "method": "openwallet",
  "params": ["1.json", "1111"],
  "id": 1
}
```

响应正文：

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": true
}
```

响应说明：

返回是否成功打开钱包。