<div align="center">  
<h1>NEO3 开发指南</h1>
<img src="images/neo-rebranding.png" alt="NEO3 Development Guide" height="150">
<p>NEO3 开发指南， 协助NEO3早期基础工具开发者完成NEO3底层建设</p>
</div>

## 目录 
- [钱包](cn/钱包)
- [交易](cn/交易)
- [RPC](cn/RPC)
- [合约](cn/合约)
- [虚拟机](cn/虚拟机)



## NEO3变更部分

### 钱包

- [脚本地址](cn/钱包#地址)由Neo2.x中的 `0x21 + publicKey(compressed, 33 bytess) + 0xac` 变成NEO3中的 `0x21 + publicKey(compressed, 33 bytes)+ 0x68 + 0x747476aa`

### 交易

- 更新了[交易结构](#交易结构)，包括删除了字段inputs、outputs，新增字段validUntilBlock、witnesses等
- 弃用了UTXO模型，仅保留有账户余额模型
- 取消了每笔交易10 GAS的免费额度，[系统费](cn/交易#systemfee)用总额受合约脚本的指令数量和指令类型影响
- [地址脚本](cn/交易#验证脚本)发生了变动，不再使用 `Opcode.CheckSig`, `OpCode.CheckMultiSig` 指令， 换成使用互操作服务调用，即`SysCall "Neo.Crypto.CheckSig".hash2uint`, `SysCall "Neo.Crypto.CheckMultiSig".hash2unit` 方式

### RPC

- 取消了 `claimgas`, `dumpprivkey`, `getaccountstate`, `getapplicationlog`, `getassetstate`, `getbalance`, `getclaimable`, `getmetricblocktimestamp`, `getnep5balances`, `getnep5transfers`, `getnewaddress`, `gettxout`, `getunclaimed`, `getunclaimedgas`, `getunspents`, `getwalletheight`, `importprivkey`, `invoke`, `listaddress`, `sendfrom`, `sendtoaddress`, `sendmany` 等API指令。
- 重新定义了[getblockheader](cn/RPC/api/getblockheader.md)，[getrawmempool](cn/RPC/api/getrawmempool.md)等API指令的调用方式。
- 更新了[getblock](cn/RPC/api/getblock.md)，[getblockheader](cn/RPC/api/getblockheader.md)，[getrawtransaction](cn/RPC/api/getrawtransaction.md)，[getversion](cn/RPC/api/getversion.md)，[getcontractstate](cn/RPC/api/getcontractstate.md)等API指令的返回内容。

### 智能合约

- 增加[Manifest](cn/合约#manifest)文件来描述合约的特性
- 增加[原生合约](cn/合约#原生合约)
- 减少了OpCode和互操作接口的[系统费](cn/合约#费用)
- 增加合约对[网络资源访问](cn/合约#网路资源访问-待补充)的支持
- 新增`System`触发器类型

### 虚拟机

- 取消了`APPCALL`, `TAILCALL`, `SHA1`, `SHA256`, `HASH160`, `HASH256`, `CHECKSIG`, `VERIFY`, `CHECKMULTISIG`, `CALL_I`, `CALL_E`, `CALL_ED, `CALL_ET, `CALL_EDT` 等Opcode。
- 新增了 [DUPFROMALTSTACKBOTTOM](cn/虚拟机#栈操作) 等Opcode。



