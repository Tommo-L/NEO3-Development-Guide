# 智能合约

<!-- TOC -->

- [智能合约](#智能合约)
- [智能合约](#智能合约-1)
    - [NEO3变更部分](#neo3变更部分)
    - [Manifest](#manifest)
    - [Trigger](#trigger)
    - [原生合约](#原生合约)
        - [介绍](#介绍)
            - [NeoToken](#neotoken)
            - [GasToken](#gastoken)
            - [PolicyToken](#policytoken)
        - [原生合约 部署](#原生合约-部署)
        - [原生合约 调用](#原生合约-调用)
    - [Interop Service](#interop-service)
        - [互操作服务原理](#互操作服务原理)
        - [互操作服务使用](#互操作服务使用)
        - [System空间](#system空间)
            - [System.ExecutionEngine.GetScriptContainer](#systemexecutionenginegetscriptcontainer)
            - [System.ExecutionEngine.GetExecutingScriptHash](#systemexecutionenginegetexecutingscripthash)
            - [System.ExecutionEngine.GetCallingScriptHash](#systemexecutionenginegetcallingscripthash)
            - [System.ExecutionEngine.GetEntryScriptHash](#systemexecutionenginegetentryscripthash)
            - [System.Runtime.Platform](#systemruntimeplatform)
            - [System.Runtime.GetTrigger](#systemruntimegettrigger)
            - [System.Runtime.CheckWitness](#systemruntimecheckwitness)
            - [System.Runtime.Notify](#systemruntimenotify)
            - [System.Runtime.Log](#systemruntimelog)
            - [System.Runtime.GetTime](#systemruntimegettime)
            - [System.Runtime.Serialize](#systemruntimeserialize)
            - [System.Runtime.Deserialize](#systemruntimedeserialize)
            - [System.Runtime.GetInvocationCounter](#systemruntimegetinvocationcounter)
            - [System.Runtime.GetNotifications](#systemruntimegetnotifications)
            - [System.Crypto.Verify](#systemcryptoverify)
            - [System.Blockchain.GetHeight](#systemblockchaingetheight)
            - [System.Blockchain.GetHeader](#systemblockchaingetheader)
            - [System.Blockchain.GetBlock](#systemblockchaingetblock)
            - [System.Blockchain.GetTransaction](#systemblockchaingettransaction)
            - [System.Blockchain.GetTransactionHeight](#systemblockchaingettransactionheight)
            - [System.Blockchain.GetContract](#systemblockchaingetcontract)
            - [System.Header.GetIndex](#systemheadergetindex)
            - [System.Header.GetHash](#systemheadergethash)
            - [System.Header.GetPrevHash](#systemheadergetprevhash)
            - [System.Header.GetTimestamp](#systemheadergettimestamp)
            - [System.Block.GetTransactionCount](#systemblockgettransactioncount)
            - [System.Block.GetTransactions](#systemblockgettransactions)
            - [System.Block.GetTransaction](#systemblockgettransaction)
            - [System.Transaction.GetHash](#systemtransactiongethash)
            - [System.Contract.Call](#systemcontractcall)
            - [System.Contract.Destroy](#systemcontractdestroy)
            - [System.Storage.GetContext](#systemstoragegetcontext)
            - [System.Storage.GetReadOnlyContext](#systemstoragegetreadonlycontext)
            - [System.Storage.Get](#systemstorageget)
            - [System.Storage.Put](#systemstorageput)
            - [System.Storage.PutEx](#systemstorageputex)
            - [System.Storage.Delete](#systemstoragedelete)
            - [System.StorageContext.AsReadOnly](#systemstoragecontextasreadonly)
        - [Neo空间](#neo空间)
            - [Neo.Native.Deploy](#neonativedeploy)
            - [Neo.Crypto.CheckSig](#neocryptochecksig)
            - [Neo.Crypto.CheckMultiSig](#neocryptocheckmultisig)
            - [Neo.Header.GetVersion](#neoheadergetversion)
            - [Neo.Header.GetMerkleRoot](#neoheadergetmerkleroot)
            - [Neo.Header.GetNextConsensus](#neoheadergetnextconsensus)
            - [Neo.Transaction.GetScript](#neotransactiongetscript)
            - [Neo.Transaction.GetWitnesses](#neotransactiongetwitnesses)
            - [Neo.Witness.GetVerificationScript](#neowitnessgetverificationscript)
            - [Neo.Account.IsStandard](#neoaccountisstandard)
            - [Neo.Contract.Create](#neocontractcreate)
            - [Neo.Contract.Update](#neocontractupdate)
            - [Neo.Contract.GetScript](#neocontractgetscript)
            - [Neo.Contract.IsPayable](#neocontractispayable)
            - [Neo.Storage.Find](#neostoragefind)
            - [Neo.Enumerator.Create](#neoenumeratorcreate)
            - [Neo.Enumerator.Next](#neoenumeratornext)
            - [Neo.Enumerator.Value](#neoenumeratorvalue)
            - [Neo.Enumerator.Concat](#neoenumeratorconcat)
            - [Neo.Iterator.Create](#neoiteratorcreate)
            - [Neo.Iterator.Key](#neoiteratorkey)
            - [Neo.Iterator.Keys](#neoiteratorkeys)
            - [Neo.Iterator.Values](#neoiteratorvalues)
            - [Neo.Iterator.Concat](#neoiteratorconcat)
            - [Neo.Json.Serialize](#neojsonserialize)
            - [Neo.Json.Deserialize](#neojsondeserialize)
    - [费用](#费用)
    - [网路资源访问 (待补充)](#网路资源访问-待补充)
    - [合约调用](#合约调用)
    - [合约升级](#合约升级)
    - [合约销毁](#合约销毁)

<!-- /TOC -->

# 智能合约

## NEO3变更部分

NEO3中所有交易都是智能合约的调用，除了一些互操作指令和OpCode的调整，NEO3中比较大的特性包括：

- 增加[Manifest](#manifest)文件来描述合约的特性
- 增加[原生合约](#原生合约)
- 减少了OpCode和互操作接口的[系统费](#费用)
- 增加合约对[网络资源访问](#网路资源访问-待补充)的支持。
- 新增`System`触发器类型

## Manifest
现在每个合约都需要对应的manifest文件描述其属性，其内容包括：Groups， Features， ABI，Permissions， Trusts， SafeMethods。

一个manifest内容示例如下：
```json
{
"groups": [
  {
    "pubKey": "",
    "signature": ""
  }
],
"features": {
  "storage": false,
  "payable": false
},
"abi": null,
"permissions": [
  {
    "contract": "*",
    "methods": "*"
  }
],
"trusts": "*",
"safemethods": "*"
}
```
- **Groups**：声明本合约所归属的组，可以支持多个, 每一个组由一个公钥和签名表示。
- **Features**：声明智能合约的特性。其中属性值 storage 表明合约可以访问存储区，payable 表明合约可以接受资产的转入。
- **ABI**：声明智能合约的接口信息，可以参考[NEP-3](https://github.com/neo-project/proposals/blob/master/nep-3.mediawiki)。接口的基础属性包括:
  - Hash: 16进制编码的合约脚本哈希;
  - EntryPoint: 提供了合约入口方法的详细信息，包括方法名、方法参数以及方法返回值;
  - Methods: 由合约方法的详细信息构成的数组;
  - Events: 由合约事件构成的数组。基于 ABI 信息，可实现合约间的相互调用。
- **Permissions**：声明合约可调用的其他合约和方法。执行合约调用时，会检查 Permission 中配置的权限，若没有相应权限，则调用操作会执行失败。
- **Trusts**：声明合约可以被哪些合约或者哪些合约组安全地调用。
- **SafeMethods**：声明哪些方法是SafeMethod，SafeMethod通常是不会修改存储区，只读取区块链数据的方法，被调用时不会给用户接口返回警告信息。

## Trigger
触发器可以使合约根据不同的使用场景执行不同的逻辑。

- **System** 此触发器为NEO3新增触发器类型。当节点收到新区块后触发，目前只会触发原生合约的执行。当节点收到新区块，持久化之前会调用所有原生合约的onPersist方法，触发方式为System。
- **Application** 应用触发器的目的在于将该合约作为应用函数进行调用，应用函数可以接受多个参数，对区块链的状态进行更改，并返回任意类型的返回值。以下是一个简单的c#智能合约：

```csharp
public static Object Main(string operation, params object[] args)
{
  if (Runtime.Trigger == TriggerType.Application)
  {
      if (operation == "FunctionA") return FunctionA(args);
  }  
}
public static bool FunctionA(params object[] args)
{
  //some code  
}
```

NEO3中所有交易都为合约的调用，当一笔交易被广播和确认后，智能合约由共识节点执行，普通节点在转发交易时不执行智能合约。智能合约执行成功不代表交易的成功，而交易的成功也不决定智能合约执行的成功。

- **Verification** 验证触发器的目的在于将该合约作为验证函数进行调用，验证函数可以接受多个参数，并且应返回有效的布尔值，标志着交易或区块的有效性。

当你想从 A 账户向 B 账户进行转账时，会触发验证合约，所有收到这笔交易的节点（包括普通节点和共识节点）都会验证 A 账户的合约，如果返回值为 true，即转账成功。如果返回 false，即转账失败。

如果鉴权合约执行失败，这笔交易将不会被写入区块链中。

下面的代码就是一个验证合约的简单示例，当条件 A 满足时，返回 true，即转账成功。否则返回 false，转账失败。

```csharp
using Neo.SmartContract.Framework;
using Neo.SmartContract.Framework.Neo;

public static bool Main(byte[] signature)
{
    if (/*条件A*/)
        return true;
    else
        return false;
}
```

下面的这段代码的作用与上面的基本相同，但对运行时的触发器进行了判断，仅当触发器为验证触发器时执行验证部分的代码，这在复杂的智能合约中很有用，如果一个智能合约实现了多种触发器，应该在 Main 方法中对触发器进行判断。

```csharp
using Neo.SmartContract.Framework;
using Neo.SmartContract.Framework.Neo;

public static bool Main(byte[] signature)
{
    if (Runtime.Trigger == TriggerType.Verification)
    {
        if (/*条件A*/)
                return true;
            else
                return false;
    }  
}
```

## 原生合约
### 介绍
原生合约是直接在原生代码中执行，而不是在虚拟机中运行的合约。原生合约公开其服务名称，供其他合约调用。目前已有的原生合约包括NeoToken，GasToken，PolicyToken。

#### NeoToken

简称 NEO，是Neo的治理代币，用于执行对 Neo 网络的管理权，符合 NEP-5 标准。NEO 的总量为 1 亿，最小单位为 1，且不可分割。Neo 在创世块中注册生成。具体接口细节如下：

- **unClaimGas**：获取到指定高度，未claim的GAS数量

```csharp
[ContractMethod(0_03000000, 
  ContractParameterType.Integer, 
  ParameterTypes = new[] { 
  ContractParameterType.Hash160, 
  ContractParameterType.Integer 
}, 
ParameterNames = new[] { "account", "end" }, 
SafeMethod = true)]
private StackItem UnclaimedGas(ApplicationEngine engine, VMArray args)
```




<table class="mytable">
<tr >
<th rowspan="3">参数列表</th>
<th >参数名称</th>
<th >参数类型</th>
<th  >描述</th>
</tr>
<tr >
<td>account</td>
<td >Hash60</td>
<td>要查询账户的ScriptHash</td>
</tr>
<tr >
<td >end</td>
<td >Integer</td>
<td >要查询的截止高度</td>
</tr>
<tr >
<th  rowspan="2">返回值</th>
<th>返回值类型</th>
<th colspan="2">描述</th>
</tr>
<tr >
<td  >registerValidator</td>
<td colspan="2" >未claimGAS数量 </td>
</tr>
<tr >
<th >费用（GAS）</th>
<td colspan="3" >0.03</td>
</tr>
</table>



- **RegisterValidator**：注册验证人

```csharp
[ContractMethod(0_05000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { 
  ContractParameterType.PublicKey 
}, 
ParameterNames = new[] { "pubkey" })]
private StackItem RegisterValidator(ApplicationEngine engine, VMArray args)
```
<table class="mytable">
<tr >
<th rowspan="2">参数列表</th>
<th >参数名称</th>
<th >参数类型</th>
<th  >描述</th>
</tr>
<tr >
<td>pubKey</td>
<td >PublicKey</td>
<td>要注册验证人的账户的公钥</td>
</tr>
<tr >
<th  rowspan="2">返回值</th>
<th  >返回值类型</th>
<th colspan="2">描述</th>
</tr>
<tr >
<td >Boolean</td>
<td colspan="2" >注册结果，true：成功， false：失败 </td>
</tr>
<tr >
<th >费用（GAS）</th>
<td colspan="3">0.05</td>
</tr>
</table>


- **getRegisteredValidators**：获取当前注册的验证人和备选节点信息

```csharp
[ContractMethod(1_00000000, 
  ContractParameterType.Array,
  SafeMethod = true)]
private StackItem GetRegisteredValidators(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
  <tr >
      <th >参数列表</th>
      <th colspan="2" >无参数</th>
  </tr>
  <tr >
      <th  rowspan="2">返回值</th>
      <th >返回值类型</th>
      <th   colspan="2">描述</th>
  </tr>
  <tr >
      <td  >Array</td>
      <td colspan="2" >所有验证人和备选节点信息 </td>
  </tr>
  <tr >
      <th >费用（GAS）</th>
      <td colspan="2" >1.00</td>
  </tr>
</table>



- **getValidators**: 获取当前区块所有验证人信息

```csharp
[ContractMethod(1_00000000, 
  ContractParameterType.Array, 
  SafeMethod = true)]
private StackItem GetValidators(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
<th >参数列表</th>
<th colspan="2">无参数</th>
</tr>
<tr >
<th  rowspan="2">返回值</th>
<th  >返回值类型</th>
<th  colspan="2">描述</th>
</tr>
<tr >
<td >Array</td>
<td colspan="2"  >所有验证人信息 </td>
</tr>
<tr >
<th >费用（GAS）</th>
<th colspan="2">1.00</th>
</tr>
</table>


- **getNextBlockValidators**: 获取下一个区块的验证人信息

```csharp
[ContractMethod(1_00000000, 
  ContractParameterType.Array, 
  SafeMethod = true)]
private StackItem GetNextBlockValidators(ApplicationEngine engine, VMArray args)
```
<table class="mytable">
<tr >
  <th >参数列表</th>
  <th colspan="2">无参数</th>
  </tr>
<tr >
  <th  rowspan="2">返回值</th>
  <th >返回值类型</th>
  <th  colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Array</td>
  <td colspan="2"  >所有验证人信息 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="2">1.00</th>
  </tr>
</table>


- **vote**：投票选举验证人

```csharp
[ContractMethod(5_00000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] {         
  ContractParameterType.Hash160, 
  ContractParameterType.Array }, 
  ParameterNames = new[] { "account", "pubkeys" })]
private StackItem Vote(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
  <tr >
  <th rowspan="3">参数列表</th>
  <th >参数名称</th>
  <th >参数类型</th>
  <th  >描述</th>
  </tr>
  <tr >
  <td>account</td>
  <td >Hash60</td>
  <td>投票人的ScriptHash</td>
  </tr>
  <tr >
  <td >pubkeys</td>
  <td >Array</td>
  <td >投给验证人的公钥</td>
  </tr>
<tr >
  <th  rowspan="2">返回值</th>
  <th >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Boolean</td>
  <td colspan="2"  >投票结果，true：成功， false：失败 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="3">5.00</th>
  </tr>
</table>



- **name***： Token的名称

```csharp
[ContractMethod(0, 
  ContractParameterType.String, 
  Name = "name", 
  SafeMethod = true)]
protected StackItem NameMethod(ApplicationEngine engine, VMArray args)
```

<table style="width:65%; text-align:center">
<tr >
  <th >参数列表</th>
  <th colspan="2">无参数</th>
  </tr>
<tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >String</td>
  <td colspan="2"  >Token的名称 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <td colspan="2" >0.00</td>
  </tr>
</table>

- **symbol***：Token的简称

```csharp
[ContractMethod(0, 
  ContractParameterType.String, 
  Name = "symbol", 
  SafeMethod = true)]
protected StackItem SymbolMethod(ApplicationEngine engine, VMArray args)
```
<table style="width:65%; text-align:center">
<tr >
  <th >参数列表</th>
  <th colspan="2" >无参数</th>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >String</td>
  <td colspan="2"  >Token的简称 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="2" >0.00</th>
  </tr>
</table>

- **decimals***: Token的计算精度

```csharp
[ContractMethod(0, 
  ContractParameterType.Integer, 
  Name = "decimals", 
  SafeMethod = true)]
protected StackItem DecimalsMethod(ApplicationEngine engine, VMArray args)
```
<table style="width:65%; text-align:center">
<tr >
  <th >参数列表</th>
  <th colspan="2" >无参数</th>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Unit</td>
  <td colspan="2"  >Token的计算精度 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="2" >0.00</th>
  </tr>
</table>

- **totalSupply***: 总发行量

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  SafeMethod = true)]
protected StackItem TotalSupply(ApplicationEngine engine, VMArray args)
```
<table style="width:65%; text-align:center">
<tr >
  <th >参数列表</th>
  <th colspan="2" >无参数</th>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >BigInteger</td>
  <td colspan="2"  >Token的总发行量 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="2" >0.01</th>
  </tr>
</table>


- **balanceOf***: 指定地址的Token余额

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  ParameterTypes = new[] { ContractParameterType.Hash160 }, 
  ParameterNames = new[] { "account" }, 
  SafeMethod = true)]
protected StackItem BalanceOf(ApplicationEngine engine, VMArray args)
```

<table style="width:65%; text-align:center">
<tr >
  <th rowspan="2">参数列表</th>
  <th >参数名称</th>
  <th >参数类型</th>
  <th  >描述</th>
  </tr>
  <tr >
  <td>account</td>
  <td >Hash60</td>
  <td>要查询账户的ScriptHash</td>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >BigInteger</td>
  <td colspan="2" >余额数值</td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="3" >0.01</th>
  </tr>
</table>

- **transfer***: 转账

```csharp
[ContractMethod(0_08000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { 
  ContractParameterType.Hash160, 
  ContractParameterType.Hash160, 
  ContractParameterType.Integer 
}, 
ParameterNames = new[] { "from", "to", "amount" })]
protected StackItem Transfer(ApplicationEngine engine, VMArray args)
```

<table style="width:65%; text-align:center">
  <tr >
  <th rowspan="4">参数列表</th>
  <th >参数名称</th>
  <th >参数类型</th>
  <th  >描述</th>
  </tr>
  <tr >
  <td>from</td>
  <td >Hash160</td>
  <td>转出账户的ScriptHash</td>
  </tr>
  <tr >
  <td>to</td>
  <td >Hash160</td>
  <td>转入账户的ScriptHash</td>
  </tr>
  <tr >
  <td>amount</td>
  <td >Integer</td>
  <td>转账的Token数量</td>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Boolean</td>
  <td colspan="2" >转账结果，true：成功，false：失败</td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="3" >0.08</th>
  </tr>
</table>

> 标*的方法为[NEP-5](https://github.com/neo-project/proposals/blob/master/nep-5.mediawiki)标准接口

#### GasToken

简称 GAS，Neo 的燃料代币，用于支付手续费，符合 NEP-5 标准。Neo 网络上的 交易费用和共识节点激励均以 GAS 来支付。GAS 总量 1 亿，最小单位 0.00000001。 GAS 在创始块中被注册，但未分发。  
GAS 的分发机制: 生成一个新区块时会伴随产生新的 GAS，所生成的 GAS 和系统 费会分发给 NEO 的持有者。用户地址中的 NEO 余额发生变化时会自动进行 GAS 的计 算和分发，计算方法如下图所示:  

![GAS distribution](../../images/GAS_distribution.png) 

其中，m 表示用户上一次提取 GAS 时所处的区块高度，n 为当前的区块高度，NEO 表示 m 至 n 期间用户持有的 NEO 的数量。BlockBonus 代表每个区块可以提取的 GAS 量，具体细节可参看经济模型部分。SystemFee 代表该区块中所有交易的系统费之和。

GasToken的详细接口介绍如下：

- **getSysFeeAmount**: 获取截止到某一高度的系统费总和

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  ParameterTypes = new[] { ContractParameterType.Integer }, 
  ParameterNames = new[] { "index" }, 
  SafeMethod = true)]
private StackItem GetSysFeeAmount(ApplicationEngine engine, VMArray args)
```

<table style="width:65%; text-align:center">
  <tr >
  <th rowspan="2">参数列表</th>
  <th >参数名称</th>
  <th >参数类型</th>
  <th  >描述</th>
  </tr>
  <tr >
  <td>index</td>
  <td >Integer</td>
  <td>要查询的高度</td>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Integer</td>
  <td colspan="2"  >系统费总值 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="3" >0.01</th>
  </tr>
</table>

- **name***: Token的名称

```csharp
[ContractMethod(0, 
  ContractParameterType.String, 
  Name = "name", 
  SafeMethod = true)]
protected StackItem NameMethod(ApplicationEngine engine, VMArray args)
```
<table style="width:65%; text-align:center">
<tr >
  <th >参数列表</th>
  <th colspan="2" >无参数</th>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >String</td>
  <td colspan="2"   >Token的名称 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="2" >0.00</th>
  </tr>
</table>

- **symbol**: Token的简称

```csharp
[ContractMethod(0, 
  ContractParameterType.String, 
  Name = "symbol", 
  SafeMethod = true)]
protected StackItem SymbolMethod(ApplicationEngine engine, VMArray args)
```

<table style="width:65%; text-align:center">
<tr >
  <th >参数列表</th>
  <th colspan="2" >无参数</th>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >String</td>
  <td colspan="2"  >Token的简称 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="2" >0.00</th>
  </tr>
</table>

- **decimals***: Token的计算精度

```csharp
[ContractMethod(0, 
  ContractParameterType.Integer, 
  Name = "decimals", 
  SafeMethod = true)]
protected StackItem DecimalsMethod(ApplicationEngine engine, VMArray args)
```
<table style="width:65%; text-align:center">
<tr >
  <th >参数列表</th>
  <th colspan="2" >无参数</th>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Unit</td>
  <td colspan="2"  >Token的计算精度 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="2" >0.00</th>
  </tr>
</table>

- **totalSupply***: 总发行量

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  SafeMethod = true)]
protected StackItem TotalSupply(ApplicationEngine engine, VMArray args)
```
<table style="width:65%; text-align:center">
<tr >
  <th >参数列表</th>
  <th colspan="2" >无参数</th>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >BigInteger</td>
  <td colspan="2"  >Token的总发行量 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="2" >0.01</th>
  </tr>
</table>

- **balanceOf***: 指定地址的Token余额

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  ParameterTypes = new[] { ContractParameterType.Hash160 }, 
  ParameterNames = new[] { "account" }, 
  SafeMethod = true)]
protected StackItem BalanceOf(ApplicationEngine engine, VMArray args)
```

<table style="width:65%; text-align:center">
<tr >
  <th rowspan="2">参数列表</th>
  <th >参数名称</th>
  <th >参数类型</th>
  <th  >描述</th>
  </tr>
  <tr >
  <td>account</td>
  <td >Hash60</td>
  <td>要查询账户的ScriptHash</td>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Integer</td>
  <td colspan="2"  >余额数值</td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="3" >0.01</th>
  </tr>
</table>

- **transfer***: 转账

```csharp
[ContractMethod(0_08000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { 
  ContractParameterType.Hash160, 
  ContractParameterType.Hash160, 
  ContractParameterType.Integer 
}, 
ParameterNames = new[] { "from", "to", "amount" })]
protected StackItem Transfer(ApplicationEngine engine, VMArray args)
```
<table style="width:65%; text-align:center">
  <tr >
  <th rowspan="4">参数列表</th>
  <th >参数名称</th>
  <th >参数类型</th>
  <th  >描述</th>
  </tr>
  <tr >
  <td>from</td>
  <td >Hash160</td>
  <td>转出账户的ScriptHash</td>
  </tr>
  <tr >
  <td>to</td>
  <td >Hash160</td>
  <td>转入账户的ScriptHash</td>
  </tr>
  <tr >
  <td>amount</td>
  <td >Integer</td>
  <td>转账的Token数量</td>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Boolean</td>
  <td colspan="2" >转账结果，true：成功，false：失败</td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="3" >0.08</th>
  </tr>
</table>

> 标*的方法为[NEP-5](https://github.com/neo-project/proposals/blob/master/nep-5.mediawiki)标准接口

#### PolicyToken

配置共识策略的合约，保存了共识过程中相关参数，例如区块最大交易数，每字节手续费等。接口详细介绍如下：

- getMaxTransactionPerBlock: 获取每个区块最大交易数

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  SafeMethod = true)]
private StackItem GetMaxTransactionsPerBlock(ApplicationEngine engine, VMArray args)
```

<table style="width:65%; text-align:center">
<tr >
  <th >参数列表</th>
  <th colspan="2" >无参数</th>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Integer</td>
  <td colspan="2"  >区块最大交易数 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="2" >0.01</th>
  </tr>
</table>

- **GetFeePerByte**： 获取每个区块最大交易数

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  SafeMethod = true)]
private StackItem GetFeePerByte(ApplicationEngine engine, VMArray args)
```

<table style="width:65%; text-align:center">
<tr >
  <th >参数列表</th>
  <th colspan="2" >无参数</th>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Integer</td>
  <td colspan="2"  >每字节手续费 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="2" >0.01</th>
  </tr>
</table>

- **getBlockedAccounts**: 获取被加入黑名单的地址列表

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Array, 
  SafeMethod = true)]
private StackItem GetBlockedAccounts(ApplicationEngine engine, VMArray args)
```
<table style="width:65%; text-align:center">
<tr >
  <th >参数列表</th>
  <th colspan="2" >无参数</th>
  </tr>
  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Array</td>
  <td colspan="2"  >黑名单列表 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="2" >0.01</th>
  </tr>
</table>

- **setMaxTransactionsPerBlock**: 设置每个区块的最大交易数

```csharp
[ContractMethod(0_03000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { ContractParameterType.Integer }, 
  ParameterNames = new[] { "value" })]
private StackItem SetMaxTransactionsPerBlock(ApplicationEngine engine, VMArray args)
```

<table style="width:65%; text-align:center">
  <tr >
  <th rowspan="2">参数列表</th>
  <th >参数名称</th>
  <th >参数类型</th>
  <th  >描述</th>
  </tr>
  <tr >
  <td>value</td>
  <td >Integer</td>
  <td>要设置的数值</td>
  </tr>

  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Boolean</td>
  <td colspan="2"  >结果，true：设置成功， false：设置失败 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="3" >0.03</th>
  </tr>
</table>

- **setFeePerByte**：设置每比特手续费

```csharp
[ContractMethod(0_03000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { ContractParameterType.Integer }, 
  ParameterNames = new[] { "value" })]
private StackItem SetFeePerByte(ApplicationEngine engine, VMArray args)
```
<table style="width:65%; text-align:center">
  <tr >
  <th rowspan="2">参数列表</th>
  <th >参数名称</th>
  <th >参数类型</th>
  <th  >描述</th>
  </tr>
  <tr >
  <td>value</td>
  <td >Integer</td>
  <td>要设置的数值</td>
  </tr>

  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Boolean</td>
  <td colspan="2"  >结果，true：设置成功，false：设置失败 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="3" >0.03</th>
  </tr>
</table>

- **blockAccount**：将某个地址加入黑名单

```csharp
[ContractMethod(0_03000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { ContractParameterType.Hash160 }, 
  ParameterNames = new[] { "account" })]
private StackItem BlockAccount(ApplicationEngine engine, VMArray args)
```
<table style="width:65%; text-align:center">
  <tr >
  <th rowspan="2">参数列表</th>
  <th >参数名称</th>
  <th >参数类型</th>
  <th  >描述</th>
  </tr>
  <tr >
  <td>account</td>
  <td >Hash160</td>
  <td>要列入黑名单的地址</td>
  </tr>

  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Boolean</td>
  <td colspan="2"  > 结果，true：设置成功，false：设置失败 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="3" >0.03</th>
  </tr>
</table>

- **unblockAccount**：将某个地址从黑名单移除

```csharp
[ContractMethod(0_03000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { ContractParameterType.Hash160 }, 
  ParameterNames = new[] { "account" })]
private StackItem UnblockAccount(ApplicationEngine engine, VMArray args)
```
<table style="width:65%; text-align:center">
  <tr >
  <th rowspan="2">参数列表</th>
  <th >参数名称</th>
  <th >参数类型</th>
  <th  >描述</th>
  </tr>
  <tr >
  <td>account</td>
  <td >Hash160</td>
  <td>要移出黑名单的地址</td>
  </tr>

  <tr >
  <th  rowspan="2">返回值</th>
  <th  >返回值类型</th>
  <th   colspan="2">描述</th>
  </tr>
  <tr >
  <td  >Boolean</td>
  <td colspan="2"  > true：设置成功，false：设置失败 </td>
  </tr>
  <tr >
  <th >费用（GAS）</th>
  <th colspan="3" >0.03</th>
  </tr>
</table>


**更多原生合约，敬请期待**

### 原生合约 部署
原生合约在创世区块中通过调用Neo.Native.Deploy互操作接口部署，且只能在创世块中执行。


### 原生合约 调用
原生合约的调用有两种方法, 第一种是跟普通合约一样，通过合约的脚本哈希来调用，另一种是原生合约特有的，直接通过互操作服务调用。[查看互操作服务使用](#互操作服务使用)

- **特有方法**：通过互操作接口直接调用

每个原生合约都会注册一个互操作接口，互操作接口名称为其ServiceName，都属于Neo.Native命名空间。
每个原生合约对应的ServiceName如下：

|原生合约|服务名称|
|---|---|
|NeoToken|Neo.Native.Tokens.NEO|
|GasToken|Neo.Native.Tokens.GAS|
|PolicyToken|NeoNeo.Native.Policy|

例如在c#编写智能合约中，如果需要调用GAS转账就可以如下编写：

```csharp
using Neo.SmartContract.Framework;
using Neo.SmartContract.Framework.Neo;

namespace MyContract
{
  public class MyContract: SmartContract
  {
    public static object main(string method, object[] args)
    {
      if (method == "test") {
        if (args.Length < 3) return false;
        return Native.Tokens.GAS("transfer", args);
      }
    }
  }
}
```

- **通用方法**：通过ScriptHash调用

原生合约的ScriptHash都是固定的，可以像调用其他普通合约一样用System.Contract.Call互操作接口和原生合约的ScriptHash调用。现有原生合约的ScriptHash如下：

|原生合约|脚本哈希|
|---|---|
|NeoToken| 0x43cf98eddbe047e198a3e5d57006311442a0ca15 |
|GasToken|0xa1760976db5fcdfab2a9930e8f6ce875b2d18225|
|PolicyToken|0x9c5699b260bd468e2160dd5d45dfd2686bba8b77|

具体调用细节参考[合约调用](#合约调用)

## Interop Service
互操作服务层提供了智能合约所能访问区块链数据的一些 API，利用这些 API，可以访问区块信息、交易信息、合约信息、资产信息等。除此之外互操作服务层还为每个合约提供了一个持久化存储区的功能。Neo 的每个智能合约在创建的时候都可选地启用一个私有存储区，存储区是 key-value 形式的，Neo 智能合约由合约的被调用者决定持久化存储区的上下文，而非调用者来决定。当然，调用者需要将自己的存储上下文传给被调用者（即完成授权）后，被调用者才可以执行读写操作。互操作服务分为System部分和Neo部分。

### 互操作服务原理
Neo程序启动时会将一系列的互操作接口注册到虚拟机，供智能合约调用。
- **服务名称** 每个互操作都有一个名称，例如 `System.Contract.Call`。
- **映射方法** 每个互操作服务都有对应的原生方法，例如`private static bool Contract_Call(ApplicationEngine engine)`，其为在客户端中实际调用的方法
- **系统费** 每个互操作服务都有其系统费计算方法或者固定系统费。
- **触发方式** 每个互操作接口都有支持的触发方式，比如`TriggerType.All`支持所有触发器

注册时就是Neo客户端将互操作接口的服务名称，映射的具体方法，系统费计算方法，支持的触发方式封装为一个InteropDescriptor存储在Dictionary中，索引为互操作接口名称的哈希值，其计算方法为：
`BitConverter.ToUInt32(Encoding.ASCII.GetBytes(ServiceName).Sha256(), 0)`

例如，`System.Contract.Call`的哈希值为`0x627d5b52`

### 互操作服务使用 

- **智能合约** 智能合约中使用的互操作接口由对应的智能合约开发框架提供，直接调用即可，当编译时会由编译器编译成可在NeoVM中执行的操作符指令
- **交易Script** 很多时候需要手动拼接执行脚本，这时候使用互操作服务的接口名称的哈希值与SYSCALL操作符来实现。

例如，如果要通过`System.Contract.Call`来调用合约`0x43cf98eddbe047e198a3e5d57006311442a0ca15`的`name`方法：

```
PUSH0
NEWARRAY
PUSHBYTES4  6e616d65
PUSHBYTES20 0x43cf98eddbe047e198a3e5d57006311442a0ca15
SYSCALL     0x627d5b52
```
C#代码为：

```csharp
ScriptBuilder sb = new ScriptBuilder()
sb.EmitPush(0);
sb.Emit(OpCode.NEWARRAY);
sb.EmitPush(operation);
sb.EmitPush(scriptHash);
sb.EmitSysCall(InteropService.System_Contract_Call); //根据互操作索引调用
byte[] script = sb.ToArray();
```

例如在c#中可以如下方式调用：

```csharp
using Neo.SmartContract.Framework;
using Neo.SmartContract.Framework.Neo;

namespace MyContract
{
  public class MyContract: SmartContract
  {
    public static readonly byte[] GAS = HexToBytes("0xa1760976db5fcdfab2a9930e8f6ce875b2d18225");
    public static object main(string method, object[] args)
    {
      if (method == "transferGAS") {
        if (args.Length < 3) return false;
        return Contract.Call(GAS, "Transfer", args);
      }
    }
  }
}
```

互操作服务分为System部分和Neo部分，具体接口介绍如下：

### System空间

#### System.ExecutionEngine.GetScriptContainer  

| 功能描述 | 获取该智能合约的脚本容器|
|--|--|
| C#函数| byte[] GetScriptContainer() |

#### System.ExecutionEngine.GetExecutingScriptHash

| 功能描述 | 获取正在执行的智能合约的脚本哈希 |
|--|--|
| C#函数| byte[] GetExecutingScriptHash() |

#### System.ExecutionEngine.GetCallingScriptHash

| 功能描述 | 获取智能合约调用者的脚本哈希 |
|--|--|
| C#函数| byte[] GetExecutingScriptHash() |

#### System.ExecutionEngine.GetEntryScriptHash

| 功能描述 | 获得该智能合约的入口点（合约调用链的起点）的脚本散列 |
|--|-- |
| C#函数| byte[] GetEntryScriptHash() |

#### System.Runtime.Platform

| 功能描述 | 获取当前执行智能合约的平台信息 |
|--|--|
| C#函数| string Platform() |

#### System.Runtime.GetTrigger

| 功能描述 | 获取该智能合约的触发条件 |
|--|--|
| C#函数 | TriggerType Trigger() |

#### System.Runtime.CheckWitness

| 功能描述 | 验证调用该合约的容器是否被指定账户脚本哈希签名 |
|--|--|
| C#函数 | bool CheckWitness(byte[] hashOrPubKey) |

#### System.Runtime.Notify

| 功能描述 | 向执行智能合约的程序发送通知 |
|--|--|
| C#函数 | bool Notify(params object[] state) |

#### System.Runtime.Log

| 功能描述 | 向执行智能合约的程序发送通知 |
|--|--|
| C#函数 | void Log(string message) |

#### System.Runtime.GetTime

| 功能描述 | 获取当前区块的时间戳 |
|--|--|
| C#函数 | uint Time |

#### System.Runtime.Serialize

| 功能描述 | 序列化 |
|--|--|
| C#函数 | object Deserialize(this byte[] source) |

#### System.Runtime.Deserialize

| 功能描述 | 反系列化 |
|--|--|
| C#函数 | byte[] Serialize(this object source) |

#### System.Runtime.GetInvocationCounter

| 功能描述 | 获取当前合约的调用次数 |
|--|--|
| C#函数 | int GetInvocationCounter() |

#### System.Runtime.GetNotifications

| Description | 获取某合约执行的所有通知 |
|--|--|
| C# Function | StackItem[][] GetNotifications(Hash160 scriptHash) |

#### System.Crypto.Verify

| 功能描述 | 使用公钥验证消息的签名 |
|--|--|
| C#函数 | bool Verify(object message, byte[] signature, byte[] pubKey) |

#### System.Blockchain.GetHeight

| 功能描述 | 获取当前区块的高度 |
|--|--|
| C#函数 | uint GetHeight() |

#### System.Blockchain.GetHeader

| 功能描述 | 获取当前区块的区块头 |
|--|--|
| C#函数 | Header GetHeader(uint height) |
|| Header GetHeader(byte[] hash)  |

#### System.Blockchain.GetBlock

| 功能描述 | 根据区块哈希或者区块高度获取区块 |
|--|--|
| C#函数 | Block GetBlock(uint height) |
|| Block GetBlock(byte[] hash)  |

#### System.Blockchain.GetTransaction

| 功能描述 | 根据交易ID获取交易 |
|--|--|
| C#函数 | Transaction GetTransaction(byte[] hash) |

#### System.Blockchain.GetTransactionHeight

| 功能描述 | 根据交易ID获取交易所在的区块高度 |
|--|--|
| C#函数 | int GetTransactionHeight(byte[] hash) |

#### System.Blockchain.GetContract

| 功能描述 | 根据合约哈希获取合约 |
|--|--|
| C#函数 | Contract GetContract(byte[] scriptHash) |

#### System.Header.GetIndex

| 功能描述 | 从区块头中获得区块高度 |
|--|--|
| C#函数 | uint Index |

#### System.Header.GetHash

| 功能描述 | 从区块头中获得区块哈希 |
|--|--|
| C#函数 | byte[] Hash |

#### System.Header.GetPrevHash

| 功能描述 | 从区块头中获得前一个区块的哈希 |
|--|--|
| C#函数 | byte[] PreHash |

#### System.Header.GetTimestamp

| 功能描述 | 从区块头中获得时间戳 |
|--|--|
| C#函数 | byte[] Timestamp |

#### System.Block.GetTransactionCount

| 功能描述 | 获取区块中的交易数 |
|--|--|
| C#函数 | int GetTransactionCount |

#### System.Block.GetTransactions

| 功能描述 | 获取区块中的所有交易 |
|--|--|
| C#函数 | Transaction[] GetTransactions() |

#### System.Block.GetTransaction

| 功能描述 | 根据索引获取区块中某个交易 |
|--|--|
| C#函数 | Transaction[] GetTransaction(int index) |

#### System.Transaction.GetHash

| 功能描述 | 获取交易的哈希 |
|--|--|
| C#函数 | byte[] Hash |

#### System.Contract.Call 

| 功能描述 | 调用合约 |
|--|--|
| C#函数 | void Call(byte[] scriptHash, string method, object[] args) |
|  | void Call(Contract contract, string method, object[] args) |

#### System.Contract.Destroy

| 功能描述 | 销毁当前合约 |
|--|--|
| C#函数 | void Destroy() |

#### System.Storage.GetContext

| 功能描述 | 获取当前合约存储去的上下文 |
|--|--|
| C#函数 | StorageContext GetContext() |
| 说明 | StorageContext中的IsReadOnly为false |

#### System.Storage.GetReadOnlyContext

| 功能描述 | 以只读方式获取当前合约存储去的上下文 |
|--|--|
| C#函数 | StorageContext GetContext() |
| 说明 | StorageContext中的IsReadOnly为true |

#### System.Storage.Get

| 功能描述 | 根据Key值，从存储区获取对应的Value |
|--|--|
| C#函数 | byte[] Get(StorageContext context, byte[] key) |

#### System.Storage.Put

| 功能描述 | 根据存储上下文，向存储区写入Key-Value |
|--|--|
| C#函数 | byte[] Get(StorageContext context, byte[] key, byte[] value) |

#### System.Storage.PutEx

| 功能描述 | 根据存储上下文，依据flag，向存储区写入Key-Value |
|--|--|
| C#函数 | byte[] Get(StorageContext context, byte[] key, byte[] value, StorageFlags flags) |
| 说明 | StorageFlags表明了写入数据的属性，默认None，<br/>数据可以被读写。如果是Constant，数据被写入存储区后不能被修改也不能被删除|

#### System.Storage.Delete

| 功能描述 | 根据Key值，从存储区删除存储的Key-Value数据 |
|--|--|
| C#函数 | void Delete(StorageContext context, byte[] key) |
| 说明 | 如果数据的StorageFlags包含Constant，不能被删除 |

#### System.StorageContext.AsReadOnly

| 功能描述 | 将当前上下文修改为只读模式 |
|--|--|
| C#函数 | void AsReadOnly(this StorageContext context) |
| 说明 |     将StorageContext中的IsReadOnly设置为true |

### Neo空间

#### Neo.Native.Deploy

| 功能描述 | 部署并初始化所有原生合约 |
|--|--|
| 说明 | 只能在创世区块调用 |

#### Neo.Crypto.CheckSig

| 功能描述 | 根据公钥，验证当前脚本容器的签名 |
|--|--|
| C#函数 | bool CheckSig(byte[] signature, byte[] pubKey) |

#### Neo.Crypto.CheckMultiSig

| 功能描述 | 根据公钥，验证当前脚本容器的签名 |
|--|--|
| C#函数 | bool CheckMultiSig(byte[][] signatures, byte[][] pubKeys) |

#### Neo.Header.GetVersion

| 功能描述 | 从区块头中获取区块版本 |
|--|--|
| C#函数 | uint Version |

#### Neo.Header.GetMerkleRoot

| 功能描述 | 从区块头中获取MerkleTree的Root |
|--|--|
| C#函数 | byte[] MerkleRoot |

#### Neo.Header.GetNextConsensus

| 功能描述 | 从区块头中获取下一个记账合约的散列 |
|--|--|
| C#函数 | byte[] NextConsensus |

#### Neo.Transaction.GetScript

| 功能描述 | 获取交易中的脚本 |
|--|--|
| C#函数 | byte[] Script |

#### Neo.Transaction.GetWitnesses

| 功能描述 | 获取交易中的见证人 |
|--|--|
| C#函数 | Witness[] GetWitnesses(this Transaction transaction) |

#### Neo.Witness.GetVerificationScript

| 功能描述 | 获取交易中的验证脚本 |
|--|--|
| C#函数 | byte[] VerificationScript |

#### Neo.Account.IsStandard

| 功能描述 | 判断是否是标准账户 |
|--|--|
| C#函数 | bool IsStandard(byte[] scriptHash) |

#### Neo.Contract.Create

| 功能描述 | 部署合约 |
|--|--|
| C#函数 | Contract Create(byte[] script, string manifest) |
| 说明 | script合约内容不能超过1MB，manifest内容不能超过2KB |

#### Neo.Contract.Update 

| 功能描述 | 升级合约 |
|--|--|
| C#函数 | Contract Create(byte[] script, string manifest) |
| 说明 | script合约内容不能超过1MB，不能是已经部署的合约；manifest内容不能超过2KB；<br/>升级后旧合约会被摧毁 |

#### Neo.Contract.GetScript

| 功能描述 | 获取合约的脚本 |
|--|--|
| C#函数 | byte[] Script |

#### Neo.Contract.IsPayable

| 功能描述 | 获取合约是否可以接收转账 |
|--|--|
| C#函数 | bool IsPayable(this Contract contract) |

#### Neo.Storage.Find

功能描述 | 在当前存储上下文中存储区寻找指定前缀内容 |
|--|--|
| C#函数 | Iterator < byte[], byte[] > Find(StorageContext context, byte[] prefix) |

#### Neo.Enumerator.Create

| 功能描述 | 创建一个枚举器 |
|--|--|
| C#函数 | Enumerator Create(object[] array) |

#### Neo.Enumerator.Next

| 功能描述 | 获取枚举器的下一个元素 |
|--|--|
| C#函数 | bool Next(this Enumerator enumerator) |

#### Neo.Enumerator.Value

| 功能描述 | 获取枚举器当前值 |
|--|--|
| C#函数 | object Next(this Enumerator enumerator) |

#### Neo.Enumerator.Concat

| 功能描述 | 连接两个枚举器 |
|--|--|
| C#函数 | Enumerator Concat(Enumerator enumerator1, Enumerator enumerator2) |

#### Neo.Iterator.Create

| 功能描述 | 创建一个迭代器|
|--|--|
| C#函数 | Iterator Create(object[] array) |
| | Iterator Create(Dictionary<object, object> map) |

#### Neo.Iterator.Key

| 功能描述 | 获取迭代器当前Key值 |
|--|--|
| C#函数 | object Key(this Iterator it) |

#### Neo.Iterator.Keys

| 功能描述 | 获取迭代器所有Key的迭代器 |
|--|--|
| C#函数 | Iterator Keys(this Iterator it) |

#### Neo.Iterator.Values

| 功能描述 | 获取迭代器所有Value的迭代器 |
|--|--|
| C#函数 | Iterator Values(this Iterator it) |

#### Neo.Iterator.Concat

| 功能描述 | 连接两个迭代器 |
|--|--|
| C#函数 | Iterator Concat(Iterator iterator1, Iterator iterator2) |

#### Neo.Json.Serialize

| 功能描述 | 序列化JSON字符串 |
|--|--|
| C#函数 | JObject Serialize(string jsonStr) |

#### Neo.Json.Deserialize

| 功能描述 | 反序列化为JSON字符串 |
|--|--|
| C#函数 | string Deserialize(JObject jsonObj) |

## 费用

| 互操作服务 | 费用 (GAS) |
|--|--|
| System.ExecutionEngine.GetScriptContainer | 0.0000025  |
| System.ExecutionEngine.GetExecutingScriptHash| 0.000004  |
| System.ExecutionEngine.GetCallingScriptHash | 0.000004  |
| System.ExecutionEngine.GetEntryScriptHash | 0.000004  |
| System.Runtime.Platform | 0.0000025  |
| System.Runtime.GetTrigger | 0.0000025  |
| System.Runtime.CheckWitness | 0.0003  |
| System.Runtime.Notify | 0.0000025  |
| System.Runtime.Log | 0.003  |
| System.Runtime.GetTime | 0.0000025  |
| System.Runtime.Serialize | 0.001  |
| System.Runtime.Deserialize | 0.005  |
| System.Runtime.GetInvocationCounter | 0.000004  |
| System.Crypto.Verify | 0.01  |
| System.Blockchain.GetHeight | 0.000004  |
| System.Blockchain.GetHeader | 0.00007  |
| System.Blockchain.GetBlock | 0.025  |
| System.Blockchain.GetTransaction | 0.01  |
| System.Blockchain.GetTransactionHeight | 0.01  |
| System.Blockchain.GetContract | 0.01  |
| System.Header.GetIndex | 0.000004  |
| System.Header.GetHash | 0.000004  |
| System.Header.GetPrevHash | 0.000004  |
| System.Header.GetTimestamp | 0.000004  |
| System.Block.GetTransactionCount | 0.000004  |
| System.Block.GetTransactions | 0.0001  |
| System.Block.GetTransaction | 0.000004  |
| System.Transaction.GetHash | 0.000004  |
| System.Contract.Call | 0.01  |
| System.Contract.Destroy | 0.01  |
| System.Storage.GetContext | 0.000004  |
| System.Storage.GetReadOnlyContext | 0.000004  |
| System.Storage.Get | 0.01  |
| System.Storage.Put | (Key.Size + Value.Size) * GasPerByte |
| System.Storage.PutEx | (Key.Size + Value.Size) * GasPerByte |
| System.Storage.Delete | 0.01  |
| System.StorageContext.AsReadOnly | 0.000004  |
| Neo.Native.Deploy | 0 |
| Neo.Crypto.CheckSig| 0.01  |
| Neo.Crypto.CheckMultiSig| 0.01 * n |
| Neo.Header.GetVersion| 0.000004  |
| Neo.Header.GetMerkleRoot| 0.000004  |
| Neo.Header.GetNextConsensus| 0.000004  |
| Neo.Transaction.GetScript| 0.000004  |
| Neo.Transaction.GetWitnesses| 0.0001  |
| Neo.Witness.GetVerificationScript| 0.000004  |
| Neo.Account.IsStandard| 0.0003  |
| Neo.Contract.Create| (Script.Size + Manifest.Size) * GasPerByte |
| Neo.Contract.Update| (Script.Size + Manifest.Size) * GasPerByte |
| Neo.Contract.GetScript| 0.000004  |
| Neo.Contract.IsPayable| 0.000004  |
| Neo.Storage.Find| 0.01  |
| Neo.Enumerator.Create| 0.000004  |
| Neo.Enumerator.Next| 0.01  |
| Neo.Enumerator.Value| 0.000004  |
| Neo.Enumerator.Concat| 0.000004  |
| Neo.Iterator.Create| 0.000004  |
| Neo.Iterator.Key| 0.000004  |
| Neo.Iterator.Keys| 0.000004  |
| Neo.Iterator.Values| 0.000004  |
| Neo.Iterator.Concat| 0.000004  |
| Neo.Json.Serialize| 0.001  |
| Neo.Json.Deserialize| 0.005  |

## 网路资源访问 (待补充)
## 合约调用 
合约中通过开发框架提供的互操作接口[System.Contract.Call](#contract-call)来调用其他合约
例如在C#中可以如下方式调用：

```csharp
using Neo.SmartContract.Framework;
using Neo.SmartContract.Framework.Neo;

namespace MyContract
{
  public class MyContract: SmartContract
  {
    public static readonly byte[] GAS = HexToBytes("0xa1760976db5fcdfab2a9930e8f6ce875b2d18225");
    public static object main(string method, object[] args)
    {
      if (method == "transferGAS") {
        if (args.Length < 3) return false;
        return Contract.Call(GAS, "transfer", args);
      }
    }
  }
}
```

很多时候需要手动拼接执行脚本，这时候需要使用互操作接口[System.Contract.Call](#contract-call)与合约的脚本哈希来调用合约。[如何使用互操作接口](#互操作服务使用)

例如，如果要通过`System.Contract.Call`来调用合约`0x43cf98eddbe047e198a3e5d57006311442a0ca15`的`transfer`方法：

```
PUSHBYTES4  0x00e1f505
PUSHBYTES20 0xfb5fd311a3ae2b2c8ab6b63c10502f9cf58ebeed
PUSHBYTES20 0xa6b9b510a67009e61f4f95115c188437f76dd2d0
PUSH3
PACK                                                      //第三个参数，参数列表
PUSHBYTES4  0x6e616d65                                    //第二个参数，方法名
PUSHBYTES20 0x43cf98eddbe047e198a3e5d57006311442a0ca15    //第一个参数，合约脚本哈希
SYSCALL     0x627d5b52                                    //通过哈希值调用互操作接口
```

C#代码为：

```csharp
ScriptBuilder sb        = new ScriptBuilder();
UInt160 scriptHash      = UInt160.Parse("0x43cf98eddbe047e198a3e5d57006311442a0ca15");
string operation        = "transfer";
ContractParameter from  = new ContractParameter(ContractParameterType.Hash160);
ContractParameter to    = new ContractParameter(ContractParameterType.Hash160);
ContractParameter value = new ContractParameter(ContractParameterType.Integer);

from.SetValue("0xa6b9b510a67009e61f4f95115c188437f76dd2d0");
to.SetValue("0xfb5fd311a3ae2b2c8ab6b63c10502f9cf58ebeed");
value.SetValue("100000000");

sb.EmitAppCall(scriptHash, operation, new ContractParameter[]{from, to, value});
byte[] script = sb.ToArray();

public static ScriptBuilder EmitAppCall(this ScriptBuilder sb, UInt160 scriptHash, string operation, params ContractParameter[] args)
{
    for (int i = args.Length - 1; i >= 0; i--)
        sb.EmitPush(args[i]);
    sb.EmitPush(args.Length);
    sb.Emit(OpCode.PACK);
    sb.EmitPush(operation);
    sb.EmitPush(scriptHash);
    sb.EmitSysCall(InteropService.System_Contract_Call);
    return sb;
}
```

## 合约升级
合约部署以后如果需要修改合约的逻辑并且保留存储区的数据，可以使用合约升级，但是需要在旧合约中实现升级接口。在旧合约升级接口中调用[Neo.Contract.Update](#contract-update)方法。
当在旧合约中调用升级接口时，方法将会根据传入的参数构建一个新的智能合约。如果旧合约有存储区，则会将旧合约的存储区转移至新合约中。升级完成后，旧合约将会被删除，如果旧合约有存储区，则存储区也将被删除。之后旧合约将不可用，需要使用新合约的Hash值。
C#代码如下：

```csharp
using Neo.SmartContract.Framework;
using Neo.SmartContract.Framework.Neo;
using Neo.SmartContract.Framework.System;

public static bool Main(string method, params object[] args){
  if (method == "update") {
    if (args.Length < 2) return false;
    if (!Runtime.CheckWitness()) return false;
    byte[] newScript = args[0];
    string manifest = (string)args[1];
    update(newScript, manifest);
    return true;
  }
  return true;
}
void update(byte[] newScript, string manifest)
{
Contract.Update(newScript, manifest);
}
```

## 合约销毁
智能合约支持在发布之后进行销毁操作，但需要在旧合约内预留销毁接口。  
合约销毁主要调用了 `System.Contract.Destroy` 方法

C#代码如下：

```csharp
using Neo.SmartContract.Framework;
using Neo.SmartContract.Framework.System;

public static bool Main(string method, params object[] args){
    if (method == "destroy") {
      destroy();
  }
    return true;
}

void destroy(){
  Contract.Destroy();
}
```
