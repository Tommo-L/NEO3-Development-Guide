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

- [地址脚本](cn/钱包#地址)：调整通过公钥构建地址脚本的方式。
    - 普通地址：
        ```
        NEO2: 0x21 + publicKey(压缩型 33字节) + 0xac()
        NEO3: 0x21 + publicKey(压缩型 33字节) + 0x68 + 0x747476aa
        ```
    - 多方签名地址：
        ```
        NEO2: emitPush(N) + 0x21 + publicKey1(压缩型 33字节) + .... + 0x21 + publicKeym(压缩型 33字节)  + emitPush(M) + 0xae()
        NEO3: emitPush(N) + 0x21 + publicKey1(压缩型 33字节) + .... + 0x21 + publicKeym(压缩型 33字节)  + emitPush(M) + 0x68 + 0xc7c34cba
        ```

### 交易
- [交易类型]()：弃用NEO2中使用的9种交易类型，统一使用transaction类型，并重新定义其[交易结构](cn/交易#交易结构)。
- [资产](cn/合约#原生合约)：NEO和GAS代币弃用UTXO模型，使用原生合约实现的账户模型。
- 费用：
    - [系统手续费](cn/交易#systemfee)：取消了每笔交易10 GAS的免费额度，并重新定义每种虚拟机指令集所对应的执行[费用](cn/虚拟机#费用)。
    - [网络手续费](cn/交易#networkfee)：重新定于网络手续费的计算公式。

### RPC
- 调整：
    - 调用方式调整：[getblockheader](cn/RPC/api/getblockheader.md)，[getrawmempool](cn/RPC/api/getrawmempool.md)
    - 返回结果调整：[getblock](cn/RPC/api/getblock.md)，[getblockheader](cn/RPC/api/getblockheader.md)，[getrawtransaction](cn/RPC/api/getrawtransaction.md)，[getversion](cn/RPC/api/getversion.md)，[getcontractstate](cn/RPC/api/getcontractstate.md)
- 移除： `claimgas`, `dumpprivkey`, `getaccountstate`, `getapplicationlog`, `getassetstate`, `getbalance`, `getclaimable`, `getmetricblocktimestamp`, `getnep5balances`, `getnep5transfers`, `getnewaddress`, `gettxout`, `getunclaimed`, `getunclaimedgas`, `getunspents`, `getwalletheight`, `importprivkey`, `invoke`, `listaddress`, `sendfrom`, `sendtoaddress`, `sendmany` 等API指令。


### 智能合约
- 新增：
    - [Manifest文件](cn/合约#manifest)：用于描述合约的特征，随avm文件一起部署到Neo区块链。
    - [原生合约](cn/合约#原生合约)：不通过虚拟机执行，而直接运行在Neo原生代码中，目前包括：NeoToken，GasToken，以及PolicyToken。
    - [网络资源访问](cn/合约#网路资源访问)： 待补充。
    - [system 触发器](cn/合约#触发器)：用于节点收到新区块后，触发原生合约的执行。
- 调整：
    - 降低了合约执行互操作接口所对应的[系统费用](cn/合约#费用)。

### 虚拟机
- 新增：[DUPFROMALTSTACKBOTTOM](cn/虚拟机#栈操作)
- 移除：`APPCALL`, `TAILCALL`, `SHA1`, `SHA256`, `HASH160`, `HASH256`, `CHECKSIG`, `VERIFY`, `CHECKMULTISIG`, `CALL_I`, `CALL_E`, `CALL_ED, `CALL_ET`, `CALL_EDT` 等Opcodes。




