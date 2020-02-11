<div align="center">  
<h1>NEO3 开发指南</h1>
<p align="center">
  <a href="https://neo.org/">
      <img
      src="https://neo3.azureedge.net/images/logo%20files-dark.svg"
      width="250px" alt="neo-logo">
  </a>
</p>
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

- 更新
    - [地址脚本](cn/钱包#地址)：调整通过公钥构建地址脚本的方式。
        - 普通地址：

        ```
        NEO2: 0x21 + publicKey(压缩型 33字节) + 0xac
        NEO3: 0x21 + publicKey(压缩型 33字节) + 0x68 + 0x747476aa
        ```
        - 多方签名地址：

        ```
        NEO2: emitPush(N) + 0x21 + publicKey1(压缩型 33字节) + .... + 0x21 + publicKeym(压缩型 33字节)  + emitPush(M) + 0xae
        NEO3: emitPush(N) + 0x21 + publicKey1(压缩型 33字节) + .... + 0x21 + publicKeym(压缩型 33字节)  + emitPush(M) + 0x68 + 0xc7c34cba
        ```

### 交易

- 更新
    - [系统手续费](cn/交易#systemfee)：取消了每笔交易10 GAS的免费额度，并重新定义每种虚拟机指令集所对应的执行[费用](cn/虚拟机#费用)。
    - [网络手续费](cn/交易#networkfee)：重新定义网络手续费的计算公式。

- 删除
    - 交易类型：弃用NEO2中使用的9种交易类型，统一使用transaction类型，并重新定义其[交易结构](cn/交易#交易结构)。
    - [资产](cn/合约#原生合约)：NEO和GAS代币弃用UTXO模型，使用原生合约实现的账户模型。
    
### RPC

- 更新
    - 调用方式：[getblockheader](cn/RPC/api/getblockheader.md)，[getrawmempool](cn/RPC/api/getrawmempool.md)
    - 返回结果：[getblock](cn/RPC/api/getblock.md)，[getblockheader](cn/RPC/api/getblockheader.md)，[getrawtransaction](cn/RPC/api/getrawtransaction.md)，[getversion](cn/RPC/api/getversion.md)，[getcontractstate](cn/RPC/api/getcontractstate.md)

- 删除
    - `claimgas`,  `getaccountstate`, `getassetstate`, `getclaimable`, `getmetricblocktimestamp`, `gettxout`, `getunspents`, `invoke` 等API指令。


### 智能合约

- 新增
    - [Manifest文件](cn/合约#manifest)：用于描述合约的特征，随nef文件一起部署到Neo区块链。
    - [原生合约](cn/合约#原生合约)：不通过虚拟机执行，而直接运行在Neo原生代码中，目前包括：NeoToken，GasToken，以及PolicyToken。
    - [网络资源访问](cn/合约#网路资源访问)： 待补充。
    - [system 触发器](cn/合约#触发器)：用于节点收到新区块后，触发原生合约的执行。
    - 互操作服务接口：`System.Binary.Serialize`, `System.Binary.Deserialize`, `System.Contract.Create`, `System.Contract.Update`, `System.Contract.Call`, `System.Contract.CallEx`, `System.Contract.IsStandard` 等。

- 更新
    - 降低了合约执行互操作接口所对应的[系统费用](cn/合约#费用)。

- 删除
    - 互操作服务接口：`Neo.Runtime.GetTrigger`, `Neo.Runtime.CheckWitness`, `Neo.Runtime.Notify`, `Neo.Runtime.Log`, `Neo.Runtime.GetTime`, `Neo.Runtime.Serialize`, `Neo.Runtime.Deserialize`, `Neo.Blockchain.GetHeight`, `Neo.Blockchain.GetHeader`, `Neo.Blockchain.GetBlock`, `Neo.Blockchain.GetTransaction`, `Neo.Blockchain.GetTransactionHeight`, `Neo.Blockchain.GetAccount`, `Neo.Blockchain.GetValidators`, `Neo.Blockchain.GetAsset`, `Neo.Blockchain.GetContract`, `Neo.Header.GetHash`, `Neo.Header.GetVersion`, `Neo.Header.GetPrevHash`, `Neo.Header.GetMerkleRoot`, `Neo.Header.GetTimestamp`, `Neo.Header.GetIndex`,  `Neo.Header.GetConsensusData` 等。

### 虚拟机

- 新增
    - `PUSHINT`, `JMP_L`, `JMPIF_L`, `JMPIFNOT_L`, `JMPEQ`, `JMPEQ_L`, `JMPNE`, `JMPNE_L`, `JMPGT`, `JMPGT_L`, 
    `JMPGE`, `JMPGE_L`, `JMPLT`, `JMPLT_L`, `JMPLE`, `JMPLE_L`, `CALL_L`, `CALLA`, `THROWIF`, `CLEAR`, `REVERSE3`, `REVERSE4`, `REVERSEN` 等。
- 删除
    - `PUSHF`, `PUSHBYTES1`, `PUSHBYTES75`, `APPCALL`, `TAILCALL`, `XTUCK`, `XSWAP`, `FROMALTSTACK`, `TOALTSTACK`, `DUPFROMALTSTACK`, `SIZE`, `LTE`, `GTE`, `SHA1`, `SHA256`, `HASH160`, `HASH256`, `CHECKSIG`, `VERIFY`, `CHECKMULTISIG`, `ARRAYSIZE`, `CALL_I`, `CALL_E`, `CALL_ED`, `CALL_ET`, `CALL_EDT`。


*点击[此处](README.md)查看README英文版*



